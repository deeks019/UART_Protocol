# UART in Verilog

A configurable full-duplex UART with 16x oversampling, FIFOs, parity, and RTS/CTS flow control.

## Frame Format

```
      idle   START   DATA (LSB first)        PARITY    STOP
      ────┐   ┌───┬───┬───┬─────┬───┬───┐  ┌─────┐  ┌──────────
          │   │   │   │   │ ... │   │   │  │     │  │
 line     └───┘ 0 │D0 │D1 │D2   │Dn │   │  │ P   │  │ 1 / 1.5 / 2
                  └───┴───┴─────┴───┘   └──┘     └──┘
                   5 – 8 bits           optional   bit-times
```

| Field  | Size                     | Purpose                                  |
|--------|--------------------------|------------------------------------------|
| Start  | 1 bit (low)              | Wakes the receiver and syncs timing      |
| Data   | 5 / 6 / 7 / 8 bits       | Payload, sent LSB first                  |
| Parity | none / even / odd        | Single-bit error detection               |
| Stop   | 1 / 1.5 / 2 bits (high)  | Marks frame end and returns line to idle |

## Features

- **Full duplex** – independent TX and RX state machines run simultaneously.
- **16x oversampling** – the receiver samples at mid-bit for noise-tolerant reads.
- **4 baud settings** – `baud_select` picks the sample-tick divider (2, 4, 8, 16).
- **Variable data length** – 5 to 8 bits via `data_length`.
- **Parity** – none, even, or odd, generated on TX and checked on RX.
- **Stop bits** – 1, 1.5 (5-bit frames only, otherwise acts as 2), or 2.
- **16-byte FIFOs** – separate TX and RX buffers with full/empty tracking.
- **RTS/CTS flow control** – active-low; `rts` deasserts when RX FIFO is nearly full, `cts` pauses TX.
- **Error flags** – parity, framing (bad stop bit), and overrun (RX FIFO full).
- **False-start rejection** – glitches shorter than half a bit are ignored.

## Configuration

| Signal          | Values                                       |
|-----------------|----------------------------------------------|
| `baud_select`   | `00` ÷2 · `01` ÷4 · `10` ÷8 · `11` ÷16       |
| `data_length`   | `00` 5b · `01` 6b · `10` 7b · `11` 8b        |
| `parity_select` | `00` none · `01` even · `10` odd             |
| `stop_bits`     | `00` 1 · `01` 1.5 · `10` 2                   |

Baud rate = `clk / (divider × 16)`

## Ports

| Port | Dir | Description |
|------|-----|-------------|
| `clk`, `reset` | in | Clock and async active-high reset |
| `tx_start`, `tx_data[7:0]` | in | Push a byte into the TX FIFO |
| `tx_active`, `tx_done` | out | TX busy flag and end-of-frame pulse |
| `serial_tx` / `serial_rx` | out / in | Serial lines |
| `rx_read` | in | Pop the front byte of the RX FIFO |
| `rx_data[7:0]`, `rx_valid` | out | Front RX byte and data-available flag |
| `cts` / `rts` | in / out | Flow control, active-low |
| `parity_error`, `framing_error`, `overrun_error` | out | Sticky error flags, cleared on reset |

## Architecture

```
 tx_data ─► TX FIFO ─► TX FSM ─► serial_tx
                         ▲
 baud_select ─► Baud Gen ─► 16x tick ─┐
                                      ▼
 rx_data ◄─ RX FIFO ◄─ RX FSM ◄─ serial_rx

 FSM states:  IDLE → START → DATA → PARITY → STOP
```

## Testbench

Two `device` instances are wired back-to-back (TX↔RX) in `uart_tb`.

| # | Test | Checks |
|---|------|--------|
| 1 | Normal loopback | Byte sent = byte received at 16x oversampling |
| 2 | Parity | Even and odd parity bit values on the line |
| 3 | Bad oversampling | Forced early sample counter corrupts data (inspect in GTKWave) |
| 4 | RTS/CTS | RTS rises at FIFO limit, TX pauses, resumes after a read |

## Run

```bash
iverilog -o uart_sim uart.v
vvp uart_sim
gtkwave uart.vcd
```

Expected end of log: `ALL TESTS PASSED`

## Notes

- Top module is named `device`; testbench is `uart_tb`.
- Only the first stop bit is checked on RX; extra stop bits are ignored.
- Frames with a parity error are dropped, not stored.
