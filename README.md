# STM32 USB Text Encryption & Decryption

## Overview

This project implements a simple text encryption and decryption system using an **STM32F103C8T6 (Blue Pill)** microcontroller.

The STM32 communicates with a computer through USB Virtual COM Port (USB CDC). It receives text from the computer, transforms the characters using a simple custom encryption algorithm, and sends the resulting text back to the computer.

The project demonstrates USB serial communication, data processing, and basic character substitution using an STM32 microcontroller.

## Features

- USB communication between a computer and STM32.
- Receives text from a computer through USB CDC.
- Implements a custom dummy encryption algorithm.
- Supports both encryption and decryption.
- Sends the processed text back to the computer.
- Uses a GPIO pin to select between encryption and decryption modes.

## Hardware Requirements

- STM32F103C8T6 (Blue Pill) development board.
- USB cable for connecting the STM32 to a computer.
- Computer with a serial terminal application.

## Software Requirements

- STM32CubeIDE
- STM32CubeMX (configuration is included in the project)
- A serial terminal application (such as PuTTY or Tera Term).

## How It Works

The system follows a simple communication process:

1. The computer sends a text message to the STM32 through USB CDC.
2. The STM32 receives the incoming data and stores it in a buffer.
3. The program checks the state of GPIO pin PB12 to determine the operating mode.
4. Based on the selected mode, the STM32 performs encryption or decryption.
5. The processed text is transmitted back to the computer through USB CDC.

### Encryption Algorithm

The project uses a 27-character matrix containing:

- Letters A–Z
- Space character

The characters are represented as a linear sequence of 27 positions.

A fixed encryption key of `3` is used.

For each character, the algorithm calculates a new position using the current character position, encryption key, and a changing position offset.

The encryption formula is:

`Encrypted_Index = (Original_Index + Key + Offset) % 27`

The decryption formula is:

`Original_Index = (Encrypted_Index - Key - Offset + 27) % 27`

The offset changes after processing each character, making the transformation position-dependent.

**Note:** This is a demonstration algorithm for learning purposes, not a secure encryption method.

## Operating Modes

The operating mode is selected using GPIO pin PB12.

| PB12 State | Operation |
|---|---|
| HIGH | Encryption |
| LOW | Decryption |

PB12 is configured as an input with an internal pulldown resistor.

## Supported Characters

The current implementation supports:

- Uppercase English letters (A–Z)
- Space character

Other characters, including lowercase letters, numbers, and punctuation, are not supported by the character mapping.

## Project Structure

```text
ComData/
│
├── Core/
│   ├── Inc/
│   │   └── main.h
│   │
│   ├── Src/
│   │   ├── main.c
│   │   ├── stm32f1xx_hal_msp.c
│   │   ├── stm32f1xx_it.c
│   │   ├── syscalls.c
│   │   └── sysmem.c
│   │
│   └── Startup/
│
├── Drivers/
│   ├── CMSIS/
│   └── STM32F1xx_HAL_Driver/
│
├── USB_DEVICE/
│   ├── App/
│   │   ├── usbd_cdc_if.c
│   │   └── usbd_cdc_if.h
│   │
│   └── Target/
│
├── ComData.ioc
└── STM32F103C8TX_FLASH.ld
```

## How to Run

1. Clone or download this repository.
2. Open STM32CubeIDE.
3. Import the project into your workspace.
4. Connect the STM32F103C8T6 board to your computer using USB.
5. Build and flash the firmware onto the STM32.
6. Open a serial terminal application.
7. Select the STM32 Virtual COM Port.
8. Send a message containing uppercase letters and spaces.
9. Set PB12 HIGH for encryption or LOW for decryption.
10. The STM32 processes the message and sends the transformed text back to the computer.

## Example

Suppose the computer sends:

```text
HELLO WORLD
```

When PB12 is HIGH, the STM32 encrypts the message and returns the transformed text.

When PB12 is LOW, the STM32 decrypts the received encrypted message and returns the original text.

## Communication Interface

The project uses **USB Communication Device Class (USB CDC)** to establish a Virtual COM Port connection between the STM32 and computer.

The main USB communication functions are:

- `CDC_Receive_FS()` – Receives data from the computer.
- `CDC_Transmit_FS()` – Sends processed data back to the computer.

## Limitations

- The encryption algorithm is for demonstration and educational purposes only.
- Only uppercase English letters and spaces are supported.
- The current receive buffer is limited to 64 bytes.
- The implementation is designed for basic text communication rather than secure data transmission.

