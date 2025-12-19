# Universal Asynchronous Reciever Transmitter
# UART Implementation on FPGA (Verilog)

This project implements a UART (Universal Asynchronous Receiver Transmitter) on an FPGA using Verilog.  
The design is tested using Tera Term at 9600 baud, where data sent from a PC keyboard is received by the FPGA, stored internally, and then transmitted back to the PC.

## Introduction

UART (Universal Asynchronous Receiver Transmitter) is a full-duplex asynchronous serial communication protocol that allows data transfer between two devices using only two signal lines:

- TX (Transmit) for sending data
- RX (Receive) for receiving data

Since UART is asynchronous, there is no shared clock between the transmitter and receiver. Instead, both devices must be configured with the same baud rate, which defines the number of bits transmitted per second.

In this project, UART communication is implemented and verified on FPGA hardware using Verilog HDL.

## UART Frame Format and Timing

Once the baud rate is configured, UART communication follows a fixed frame structure. Each bit occupies a fixed duration known as the bit period.

The frame format used in this design consists of:

- Start Bit: 1 bit (logic 0)
- Data Bits: 8 bits
- Parity Bit: Not used
- Stop Bit: 1 bit (logic 1)

### Transmission Sequence

- The TX line remains in the idle state (logic high).
- Transmission begins when the TX line is pulled low for one bit period, indicating the start bit.
- Eight data bits are transmitted least significant bit (LSB) first.
- After all data bits are sent, the TX line is driven high for the stop bit.
- The transmitter then returns to the idle state, ready for the next data frame.

The receiver interprets the incoming data using the same structure.

## Design Overview

The complete UART system is implemented using the following modules:

- Baud Rate Generator
- UART Receiver
- UART Transmitter
- Top-Level Integration Module

The design operates as a receive–store–transmit system, where data received from the PC is first stored inside the FPGA and then transmitted back to the PC.

## Baud Rate Generator

The baud rate generator produces a timing pulse derived from the system clock. This pulse defines the precise timing required for UART transmission and reception.

Both the transmitter and receiver use this baud timing signal to ensure accurate bit sampling and data transfer at 9600 baud.

## UART Receiver Module

The UART receiver continuously monitors the RX line for a start bit. Once detected, the receiver begins sampling incoming data bits.

### Receiver Operation

- Detects the start bit on the RX line
- Samples incoming data on each baud pulse
- Receives data LSB first and shifts it into an internal register
- After receiving 8 bits:
  - Reception stops
  - A valid reception signal is asserted
  - The received byte is made available for further processing

This confirms the successful reception of one complete UART data frame.

## Data Storage Mechanism

After a complete byte is received:

- The received data is stored in an internal register inside the transmitter module
- A control flag is generated to indicate that valid data is ready for transmission

This mechanism ensures that transmission begins only after reception is fully complete.

## UART Transmitter Module

The UART transmitter sends the stored data back to the PC.

### Transmitter Operation

- Waits for the reception completion flag
- Initiates transmission by sending a start bit
- Transmits stored data LSB first
- Uses an internal counter to track transmitted bits
- Sends a stop bit after all data bits are transmitted
- Asserts a transmission-complete signal and returns to the idle state

## Overall System Operation

- A key is pressed on the PC keyboard.
- Tera Term sends the corresponding 8-bit ASCII character to the FPGA at 9600 baud.
- The FPGA UART receiver detects the start bit and receives the data.
- The received byte is stored internally.
- After reception completes, the UART transmitter sends the stored data back to the PC.
- The transmitted character appears on the Tera Term terminal, confirming correct operation.

This loopback-style testing validates both UART reception and transmission on FPGA hardware.

## Summary

This project demonstrates a complete UART implementation on FPGA using Verilog. The design correctly handles asynchronous serial communication, including baud rate timing, start and stop bit handling, and LSB-first data transfer.

The modular architecture and real hardware testing make this project suitable for learning, debugging, and integration into larger digital systems.

<img width="1916" height="1010" alt="Screenshot 2025-12-17 155045" src="https://github.com/user-attachments/assets/c41aef3f-01ee-47db-9fee-e8be81d282a4" />
