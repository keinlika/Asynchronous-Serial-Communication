# UART Communication Project

## Project Objectives
- Learn how to send a UART message between your laptop and Nucleo board.
- Learn how to send a UART message between two Nucleo boards.
- Begin learning to write a lab report in LaTeX.

## Required Parts
- 1 Nucleo-L476RG development board
- 1 USB cable
- 1 breadboard (protoboard)
- 3 single-color LEDs
- 3 resistors
- Several jumper wires

## Background: UART Serial Communication
Serial communication sends data over a single wire, one bit at a time. It can be synchronous (sharing a clock) or asynchronous (no shared clock). For this project, you will be doing asynchronous serial communication.

Asynchronous serial communication requires that both the transmitter and the receiver have their own internal clocks running at the same frequency. A single "start bit" is used at the beginning of a message to allow the receiver to align its internal clock with the data. The clock used for data transmission is usually a few orders of magnitude slower than the CPU clock, so that the CPU can execute several instructions during each cycle of the transmission clock.

One of the most common protocols for asynchronous serial communication is the Universal Asynchronous Receiver Transmitter (UART) protocol. UART supports sending 7 or 8 bits at a time (usually for an ASCII character, but the data can be anything – such as measurements from a sensor – not just text). A UART message (frame) may include a parity bit for error detection. UART also includes one or two "stop bits" at the end of the frame to provide a buffer between frames. Between frames, the signal is held at a high voltage. (Keeping the voltage high when no signal is being sent makes it easier to detect if the communication line has been broken.)

The default for UART is 8 bits, no parity bit, 1 stop bit, and LSB first. Here is how such a frame appears:
```
start bit D0 D1 D2 D3 D4 D5 D6 D7 stop bit
```
As an example, if the data being sent were the ASCII code of the character `a` (0x61 or 0b01100001), the data frame sent on the wire would have the following voltage signal:
```
start bit 1 0 0 0 0 1 1 0 stop bit
```

UART supports multiple baud values as well (for bit transmission, baud = bits per second). The baud determines the data transmission rate. The inverse of the baud determines the length of a single bit. The most common baud for low-speed data transmission is 9600 bits/second.

| Standard Baud Rates | Bit Duration (inverse) |
|---------------------|------------------------|
| 1200 bits/sec       | 0.833 ms/bit           |
| 2400 bits/sec       | 0.417 ms/bit           |
| 4800 bits/sec       | 0.208 ms/bit           |
| 9600 bits/sec       | 0.104 ms/bit           |
| 19200 bits/sec      | 0.0521 ms/bit          |
| 38400 bits/sec      | 0.0260 ms/bit          |
| 57600 bits/sec      | 0.0174 ms/bit          |
| 115200 bits/sec     | 0.00868 ms/bit         |

There is also a modified version of UART that does support sharing a clock to avoid needing start and stop bits. This version is called USART (Universal Synchronous/Asynchronous Receiver Transmitter). A USART can operate in either synchronous or asynchronous mode.

## UART on the STM32-L476RG
The STM32-L476RG chip has six universal serial modules available for serial communication:
- USART1, USART2, USART3 (which support both synchronous or asynchronous communication)
- UART4, UART5 (which only support asynchronous communication)
- LPUART1 (which is a low-power UART that can be put to sleep when not in use)

### Module TxPin/RxPin pairs
- USART1: PA9/PA10 or PB6/PB7 or PG9/PG10
- USART2: PA2/PA3 or PD5/PD6
- USART3: PB10/PB11 or PC4/PC5 or PC10/PC11 or PD8/PD9
- UART4: PA0/PA1 or PC10/PC11
- UART5: PC12/PD2
- LPUART1: PB11/PB10 or PC1/PC0

On the Nucleo board, USART2 is defaulted to PA2/PA3 (labeled with D1/D0 Arduino names) for the serial connection over the USB. Therefore, those pins are reserved for USB traffic, and may not be used for other communication.

## Specifications
The system provides communication through USART2 to receive messages and USART1 to transmit and receive messages. The system processes incoming messages to control the LEDs based on the received commands.

### Inputs
- USART2 is connected to the microcontroller's pins PA2 (TX) and PA3 (RX).

### Outputs
- USART1 is connected to the microcontroller's pins PA9 (TX) and PA10 (RX).

### GPIO Pins
- LED2 output is connected to pin PA5.
- LED3 output is connected to pin PA0.
- LED4 output is connected to pin PA1.

## Test Plan and Results

| Test Scenario                  | Expected Result                  | Observed Result                  |
|--------------------------------|----------------------------------|----------------------------------|
| Board 2 sends string to Board 1| Board 2 receives the string      | Board 2 received the string      |
| Board 1, ALL ON command        | Turns all LEDs ON                | Turns all LEDs ON                |
| Board 1, ALL OFF command       | Turns all LEDs OFF               | Turns all LEDs OFF               |
| Board 2, LED 1, 2, 3 ON        | Turns ON each single LED         | Turns ON each single LED         |
| Board 2, LED 1, 2, 3 OFF       | Turns OFF each single LED        | Turns OFF each single LED        |
| Board 1, ALL ON                | All LEDs on board 2 turn ON      | All LEDs on board 2 turn ON      |
| Board 2, ALL ON                | ALL LEDs on board 1 turn ON      | ALL LEDs on board 1 turn ON      |
| Board 1, ALL OFF               | ALL LEDs board 2 turn OFF        | ALL LEDs board 2 turn OFF        |
| Board 2, ALL OFF               | ALL LEDs board 1 turn OFF        | ALL LEDs board 1 turn OFF        |
| Board 1 sends a long string to board 2 | A very long string causes a system lock | A very long string causes a system lock |

*Table 1: Test plan with expected and observed results.*

## Evidence of Correct Operation
[Video Evidence of Operation](https://vimeo.com/839390065?share=copy)
