# 🛰️ UART — Full-Featured Serial Communication Core (Verilog)

> A configurable UART transceiver with FIFO buffering, programmable framing, parity checking, and RTS/CTS hardware flow control — verified with a self-checking testbench.

---

## 🖼️ UART Frame Anatomy

```
 IDLE │ START │        DATA BITS (LSB first)        │ PARITY │ STOP(1/1.5/2) │ IDLE
 ───┐ ┌───────┬─────┬─────┬─────┬─────┬─────────────┬────────┬───────────────┐ ┌───
    └─┘  0    │ D0  │ D1  │ D2  │ ...│  D(n-1)     │   P    │       1       └─┘
              ◄─────────── 5 / 6 / 7 / 8 bits ─────►optional
              sampled @ bit center (16× oversampling)
```

---

## ✨ Features — One Line Each

| # | Feature | What it does |
|---|---------|--------------|
| 1 | **16× Oversampling** | Samples each bit 16 times and decides at the bit center for noise-robust reception. |
| 2 | **Programmable Baud** | 4 selectable baud rates (÷2, ÷4, ÷8, ÷16 tick divider) via `baud_select`. |
| 3 | **Variable Data Length** | Sends/receives 5, 6, 7, or 8 data bits per frame. |
| 4 | **Parity Control** | None / Even / Odd parity generation on TX and verification on RX. |
| 5 | **Flexible Stop Bits** | 1, 1.5 (5-bit frames only, 16550-style), or 2 stop bits. |
| 6 | **16-Byte TX FIFO** | Queue up to 16 bytes; the transmitter drains it automatically. |
| 7 | **16-Byte RX FIFO** | Buffers incoming bytes until the host reads them with `rx_read`. |
| 8 | **RTS/CTS Flow Control** | RTS goes high near-full (15/16) to pause the sender; TX only runs when CTS is low. |
| 9 | **Parity Error Flag** | `parity_error` raises and the bad frame is dropped when parity fails. |
| 10 | **Framing Error Flag** | `framing_error` raises when the stop bit is sampled low. |
| 11 | **Overrun Error Flag** | `overrun_error` raises when a byte arrives with a full RX FIFO. |
| 12 | **Status Outputs** | `tx_active`, `tx_done` pulse, and `rx_valid` give clean handshake signals. |
| 13 | **Standard Frame Order** | Start → Data (LSB first) → optional Parity → Stop, line idles high. |

---

## 🔌 Ports at a Glance

| Signal | Dir | Description |
|--------|-----|-------------|
| `clk`, `reset` | in | Clock and synchronous-active-high reset. |
| `baud_select[1:0]` | in | Picks the 16× tick rate (4 speeds). |
| `data_length[1:0]` | in | 00=5, 01=6, 10=7, 11=8 data bits. |
| `parity_select[1:0]` | in | 00=none, 01=even, 10=odd. |
| `stop_bits[1:0]` | in | 00=1, 01=1.5, 10=2 stop bits. |
| `tx_start`, `tx_data` | in | Push one byte into the TX FIFO. |
| `cts` | in | Clear-to-send, active-low (0 = may transmit). |
| `rts` | out | Ready-to-receive, active-low (1 = stop sending). |
| `serial_tx` / `serial_rx` | out/in | UART serial lines (idle high). |
| `rx_data`, `rx_valid`, `rx_read` | out/in | Front FIFO byte, data-available flag, pop strobe. |
| `parity/framing/overrun_error` | out | Sticky receiver error flags. |

---

## ⚙️ How It Works

- **Baud Generator** — divides `clk` into a `sample_tick` at 16× the bit rate.
- **Transmitter FSM** — `IDLE → START → DATA → PARITY → STOP`, shifting bits LSB-first.
- **Receiver FSM** — detects the falling edge, verifies the start bit at sample 7, then samples data at sample 15 of every bit.
- **RX policy** — only the *first* stop bit is checked (extra stop bits are ignored), matching industry practice.

---

## 🧪 Testbench — 4 Self-Checking Scenarios

| Test | Purpose |
|------|---------|
| **TEST 1** | Normal 16× oversampled loopback of `0xA5` between two UARTs. |
| **TEST 2** | Even and odd parity bit correctness checked live on the wire. |
| **TEST 3** | Forced sampling fault (`force rx_sample_count = 11`) to expose misreads. |
| **TEST 4** | RTS/CTS flow control: FIFO fills → RTS high → TX pauses → read → TX resumes. |

Run it:

```bash
iverilog -o uart uart.v uart_tb.v && vvp uart
# then view waveforms:
gtkwave uart.vcd
```

Expected output ends with: **`ALL TESTS PASSED`** ✅

---

## 📁 Files

```
├── uart.v      →  the UART device (TX + RX + FIFOs + flow control)
└── uart_tb.v   →  self-checking testbench with VCD waveform dump
```

---

<p align="center">Built with ❤️ in Verilog — start bit low, standards high.</p>
