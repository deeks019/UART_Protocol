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

The design was developed incrementally, starting from basic UART communication and progressively introducing features required for a flexible and reliable serial communication interface.

The final design explores configurable baud rate, data length, parity, stop bits, receiver oversampling, FIFO buffering, loopback operation, hardware flow control, and error detection.

---

# UART Frame Format

UART transmits data **serially, one bit at a time**, without a shared clock between the communicating devices.

A typical UART frame is structured as:

```text
  IDLE        START        DATA BITS        PARITY        STOP        IDLE
   1            0        D0 D1 ... D7        P             1           1
   │            │              │             │             │
   └────────────┴──────────────┴─────────────┴─────────────┴────────────►

                    One complete UART frame
