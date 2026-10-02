# Configurable UART Communication System

### A Feature-Rich UART RTL Design in Verilog

<p align="center">
  <b>Configurable • Reliable • Verifiable • Hardware-Oriented</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/HDL-Verilog-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/Design-RTL-orange?style=for-the-badge">
  <img src="https://img.shields.io/badge/UART-Configurable-green?style=for-the-badge">
  <img src="https://img.shields.io/badge/Verification-Testbench-purple?style=for-the-badge">
</p>

---

## About the Project

This project implements a **configurable UART (Universal Asynchronous Receiver/Transmitter) communication system using Verilog HDL**.

The design was developed incrementally, starting from basic UART communication and progressively introducing features required in a more practical digital communication interface.

The final design explores:

- Configurable baud rate
- Configurable data length
- Configurable stop bits
- Parity generation and checking
- 16× receiver oversampling
- Loopback operation
- 16-byte TX/RX FIFO buffering
- RTS/CTS hardware flow control
- Error detection and status flags
- Full-duplex and half-duplex communication

The project also includes dedicated simulation and verification stages for each major feature.

---

# System Overview

```text
                         ┌──────────────────────────┐
                         │      UART DEVICE         │
                         │                          │
                         │  ┌────────────────────┐  │
TX DATA ────────────────►│  │    TX FIFO         │  │
                         │  └─────────┬──────────┘  │
                         │            │             │
                         │       ┌────▼────┐        │
                         │       │ UART TX │────────────► TX
                         │       └─────────┘        │
                         │                          │
                         │       ┌─────────┐        │
RX DATA ◄────────────────│───────│ UART RX │        │
                         │       └────┬────┘        │
                         │            │             │
                         │  ┌─────────▼──────────┐  │
                         │  │     RX FIFO        │  │
                         │  └────────────────────┘  │
                         │                          │
                         │      RTS / CTS           │
                         │      Flow Control        │
                         └──────────────────────────┘
