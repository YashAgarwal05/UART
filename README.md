# Universal Asynchronous Reciever Transmitter

## Introduction

UART (Universal Asynchronous Receiver Transmitter) is a **full-duplex asynchronous serial communication protocol** that enables data transfer between two devices using only **two signal lines**, namely **TX** and **RX**.

- **TX (Transmit)** is used to send data  
- **RX (Receive)** is used to receive data  

UART is classified as an **asynchronous communication protocol** because there is **no shared clock signal** between the transmitting and receiving devices. Instead, both devices must operate at the same **baud rate**.

In this project, UART communication is implemented on an **FPGA using Verilog** and tested using **Tera Term at 9600 baud**, where data sent from a PC is received, stored, and transmitted back to the PC.

---

## UART Frame Format and Timing

Once a common baud rate is configured, data transmission follows a fixed UART frame structure.

Each transmitted bit occupies a fixed duration known as the **bit period**.

### Transmission Sequence

When a device begins transmission:

1. The transmitter drives the **TX line low for one bit period**
   - This low level represents the **start bit**
   - It indicates the beginning of a UART frame

2. The next **8 bits** represent the actual data
   - Data is transmitted **least significant bit first**

3. No parity bit is used in this design

4. The final bit is the **stop bit**
   - The stop bit is logic high
   - It marks the end of the data frame

The receiver interprets the incoming signal using the same frame structure.

---

## Design Overview

The UART design is divided into the following modules:

- **Baud Rate Generator**
- **UART Transmitter**
- **UART Receiver**
- **Top-Level Module**

The system works as a **receive–store–transmit loopback design**, where data received from the PC is stored inside the FPGA and then transmitted back to the PC.

---

## UART Transmitter Module

### Interface Signals

- **clk**  
  System clock input

- **reset**  
  Active-high reset signal

- **data_in**  
  8-bit data to be transmitted

- **tx**  
  Serial transmit output

- **transmitted_signal**  
  Indicates completion of data transmission

### Transmitter Operation

- Transmission starts only after valid data is available
- A **start bit** is transmitted first
- Data bits are transmitted serially **LSB first**
- An internal counter tracks the transmitted bits
- After all bits are sent, a **stop bit** is transmitted
- The transmitter then returns to the idle state

---

## UART Receiver Module

### Interface Signals

- **clk**  
  System clock input

- **reset**  
  Active-high reset signal

- **rx**  
  Serial receive input

- **data_out**  
  8-bit received data

- **recieved_signal**  
  Asserted when a full data frame is received

### Receiver Operation

- Continuously monitors the RX line
- Detects the **start bit** to begin reception
- Samples incoming data on each baud pulse
- Receives data **LSB first** and shifts it into a register
- After receiving 8 data bits:
  - Reception stops
  - A valid reception signal is generated

---

## Baud Rate Timing Generation

A dedicated baud rate generator produces a timing signal derived from the system clock.

- This timing signal synchronizes both transmission and reception
- UART operation is performed at **9600 baud**
- Baud timing is active only during transmission or reception

---

## Overall System Operation

1. A key is pressed on the PC keyboard.
2. Tera Term sends an **8-bit ASCII character** to the FPGA.
3. The UART receiver detects the start bit and receives the data.
4. The received data is stored internally.
5. The UART transmitter sends the stored data back to the PC.
6. The transmitted character appears on the terminal.

This confirms correct UART transmission and reception on FPGA hardware.

---

## Summary

This project presents a UART implementation on FPGA using Verilog. The design follows standard UART framing, operates asynchronously at a fixed baud rate, and is verified using real hardware.

The modular structure and loopback testing approach make the design suitable for learning and practical implementation.

<img width="1916" height="1010" alt="Screenshot 2025-12-17 155045" src="https://github.com/user-attachments/assets/52b3fcca-b910-46e7-ac01-59632b43cbb8" />
