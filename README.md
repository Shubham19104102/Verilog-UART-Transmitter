# UART Transmitter Design & Verification in Verilog

## 📌 Overview

This project implements and verifies an **8-bit UART (Universal Asynchronous Receiver/Transmitter) Transmitter** using Verilog HDL.

The design converts parallel 8-bit data into a serial UART data stream using a configurable clock frequency and baud rate. An FSM-based architecture is used to control the transmission of the start bit, data bits, and stop bit.

The design is verified through a Verilog behavioral testbench and simulated to observe the UART serial output, transmission status, and input data transitions.

---

## 🎯 Project Objectives

- Design an 8-bit UART transmitter using Verilog HDL.
- Implement UART serial data transmission.
- Use an FSM for reliable transmission control.
- Generate the required baud-rate timing from the system clock.
- Implement a `busy` signal to indicate an ongoing transmission.
- Verify multiple data transmissions using a behavioral testbench.
- Analyze the resulting simulation waveforms.

---

## ⚙️ Design Specifications

| Parameter | Value |
|-----------|-------|
| HDL | Verilog |
| Data Width | 8-bit |
| UART Format | 8-N-1 |
| Clock Frequency | 50 MHz |
| Baud Rate | 115200 |
| Start Bit | 1 |
| Data Bits | 8 |
| Parity | None |
| Stop Bits | 1 |
| Data Order | LSB First |

---

## 🧩 UART Frame Format

The transmitter follows the standard **8-N-1 UART protocol**.

Each transmitted byte consists of:

```text
Idle    Start       Data Bits (LSB First)       Stop
  1       0       D0 D1 D2 D3 D4 D5 D6 D7        1
```

For example, if the input data is:

8'hAA = 1010_1010

The transmitter sends the bits in LSB-first order:

Start → 0 → 1 → 0 → 1 → 0 → 1 → 0 → 1 → Stop
         D0  D1  D2  D3  D4  D5  D6  D7


🏗️ Architecture

The UART transmitter is implemented using a finite state machine with four states:

             ┌─────────────┐
             │    IDLE     │
             └──────┬──────┘
                    │ tx_start
                    ▼
             ┌─────────────┐
             │    START    │
             └──────┬──────┘
                    │
                    ▼
             ┌─────────────┐
             │     DATA    │
             └──────┬──────┘
                    │ 8 bits
                    ▼
             ┌─────────────┐
             │     STOP    │
             └──────┬──────┘
                    │
                    ▼
                  IDLE
FSM States

IDLE:
The transmitter remains idle with tx = 1 and busy = 0. When tx_start is asserted, the input data is stored and transmission begins.

START:
The transmitter drives tx low for one complete UART bit period, generating the start bit.

DATA:
The eight bits stored in data_reg are transmitted sequentially from D0 to D7.

STOP:
The transmitter drives tx high for one bit period, generating the stop bit. After completion, busy returns low and the transmitter returns to the IDLE state.

⏱️ Baud Rate Generation

The baud timing is generated from the system clock using:

localparam BAUD_COUNT = CLK_FREQ / BAUD_RATE;

For the selected parameters:

Clock Frequency = 50 MHz
Baud Rate       = 115200

Therefore:

BAUD_COUNT ≈ 434 clock cycles

Since the clock period is:

Tclk = 20 ns

one UART bit period is approximately:

434 × 20 ns ≈ 8.68 µs

which corresponds closely to a baud rate of:

≈ 115200 bits/second
🔌 Module Interface
uart_tx
Signal	Direction	Description
clk	Input	System clock
rst	Input	Active-high reset
tx_start	Input	Starts UART transmission
data_in[7:0]	Input	8-bit parallel input data
tx	Output	Serial UART output
busy	Output	High while transmission is active
🔄 Transmission Operation

The transmitter follows this sequence:

The circuit remains in the IDLE state.
data_in is loaded with the byte to be transmitted.
tx_start is asserted.
The input data is copied into data_reg.
busy becomes 1.
A UART start bit (0) is transmitted.
Eight data bits are transmitted LSB first.
A stop bit (1) is transmitted.
busy becomes 0.
The FSM returns to IDLE.
🧪 Verification

A dedicated Verilog testbench is used to verify the transmitter.

The testbench:

Generates a 50 MHz clock.
Applies reset to the DUT.
Sends multiple 8-bit data patterns.
Generates a tx_start pulse for each transmission.
Waits for the busy signal to return low.
Observes the serial tx output.
Terminates the simulation after successful transmissions.
Test Data

The testbench verifies transmission of:

8'hAA
8'hCC

The sequence is:

Reset
  ↓
Transmit 0xAA
  ↓
Wait for busy = 0
  ↓
Transmit 0xCC
  ↓
Wait for busy = 0
  ↓
Simulation complete
📊 Behavioral Simulation

The behavioral simulation waveform demonstrates:

System clock operation
Reset behavior
tx_start pulse
Parallel input data
Serial tx output
busy status during transmission

The waveform confirms that the transmitter accepts the parallel input data and converts it into the expected UART serial frame.





File Description

uart_tx.v
Contains the RTL implementation of the UART transmitter.

UART_TB.v
Contains the behavioral testbench used for functional verification.

uart-tx-behavioral-simulation.png
Shows the simulation waveform obtained from the UART transmitter verification.

🛠️ Tools Used
Verilog HDL
Vivado / XSim
RTL Simulation
Behavioral Verification

📈 Key Features
8-bit UART transmission
Standard 8-N-1 communication format
LSB-first data transmission
Configurable clock frequency
Configurable baud rate
FSM-based RTL architecture
Busy status indication
Synchronous transmission control
Behavioral simulation and verification

🚀 Future Improvements

The design can be extended with:
UART Receiver
Full UART Transceiver
Parity-bit support
Configurable data width
Multiple stop-bit options
Fractional baud-rate generation
FIFO-based UART buffering
FPGA hardware implementation
Loopback testing
📚 Applications

UART communication is widely used for:

Microcontroller communication
FPGA-to-PC communication
Debugging and logging
Embedded systems
Sensor interfaces
Serial peripheral communication
SoC and FPGA development

👨‍💻 Project Summary

This project demonstrates the complete RTL design and behavioral verification flow of a basic UART transmitter. The implementation uses an FSM to control UART frame generation and a clock-based baud-rate counter to achieve reliable serial communication at 115200 baud.The project provides a practical example of digital design, FSM implementation, serial communication protocols, RTL coding, and functional verification using Verilog HDL.
