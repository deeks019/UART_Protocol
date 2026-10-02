# Configurable UART Protocol

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

The design was developed incrementally, beginning with basic UART communication and progressively introducing features such as programmable baud rate, configurable data length, parity, oversampling, FIFO buffering, loopback, hardware flow control, and configurable stop bits.

The project focuses on **RTL design, digital communication, parameterization, and verification**.

---

# UART Frame Format

UART is an asynchronous serial communication protocol in which data is transmitted **one bit at a time**.

A typical UART frame is structured as:

```text
       IDLE
        │
        ▼
┌───────┬──────────────┬────────┬────────────┐
│ START │  DATA BITS   │ PARITY │  STOP BIT  │
│  0    │  5–8 bits    │  0/1   │     1      │
└───────┴──────────────┴────────┴────────────┘
        │
        └──────────────► Transmitted serially
