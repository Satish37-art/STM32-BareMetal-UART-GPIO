# STM32 Bare-Metal UART & GPIO Control

This repository contains bare-metal embedded C firmware for the STM32F446RE microcontroller. It demonstrates direct register-level configuration of GPIO, USART, SYSCFG, and EXTI peripherals without relying on HAL or code-generation tools like STM32CubeMX. All configurations are based directly on the STM32 RM0390 Reference Manual.

## Hardware & Tools
* **Board:** STM32 Nucleo-F446RE (ARM Cortex-M4)
* **IDE:** STM32CubeIDE 2.0.0
* **Language:** Embedded C
* **Testing:** PuTTY (Serial Monitor) at 9600 Baud

## Architecture & Implementation
The project was developed in two distinct phases to demonstrate the transition from blocking architecture to a non-blocking interrupt-driven system.

### 1. Polling Method (Base Branch)
* **GPIO Configuration:** Configured PC13 as an input for the physical User Button with software debouncing.
* **UART Configuration:** Configured PA2 (TX) and PA3 (RX) for USART2 using Alternate Function 7 (AF7).
* **Execution:** The CPU remains in a continuous `while(1)` polling loop to monitor the button state and trigger UART transmission.

### 2. EXTI Hardware Interrupts (Feature Branch)
* **SYSCFG Routing:** Enabled APB2 clock and routed EXTI Line 13 to Port C.
* **Edge Detection:** Configured the EXTI Falling Trigger Selection Register (FTSR) to detect the 3.3V to 0V drop upon button press.
* **NVIC Integration:** Enabled `EXTI15_10_IRQn` in the Nested Vectored Interrupt Controller.
* **Execution:** The CPU is freed from polling. The `EXTI15_10_IRQHandler` Interrupt Service Routine (ISR) automatically handles the button press, sends the character via UART, and clears the pending register flag.

## Pin Mapping
| Peripheral | Pin | Function |
| :--- | :--- | :--- |
| User Button | PC13 | Digital Input (Active Low) / EXTI13 |
| USART2 TX | PA2 | Alternate Function (AF7) |
| USART2 RX | PA3 | Alternate Function (AF7) |

## How to Test
1. Clone this repository and open the project in STM32CubeIDE.
2. Build the project and flash it to the Nucleo-F446RE board.
3. Open a serial terminal (like PuTTY) connected to the ST-LINK Virtual COM Port at 9600 baud rate, 8 data bits, 1 stop bit, no parity.
4. Press the blue User Button on the board to see the character 'P' transmitted to the terminal via hardware interrupts.
