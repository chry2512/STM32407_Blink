# STM32 Advanced Low-Power State Machine & LED Control Framework

A robust bare-metal firmware framework (leveraging STM32 HAL libraries) designed to implement advanced power management (**Stop Mode**), a non-blocking **Finite State Machine (FSM)** for LED control, and resilient external interrupt (EXTI) handling featuring pending flag clearing and hardware/software debouncing.

## Architecture & Core Features

### 1. Default Mode (Boot & Standard Blinking)
* Upon system boot or reset, the microcontroller enters a standard operating state featuring an **asynchronous continuous blinking** pattern. This is handled via non-blocking timing logic, completely avoiding CPU-blocking delays (`HAL_Delay`) within the main super-loop (`while(1)`).

### 2. Dual-LED Blinking Mode (Trigger K1)
* Pressing the designated external interrupt (EXTI) button transitions the state machine to a **synchronized blinking pattern across dual LEDs**. State transitions occur seamlessly due to the decoupling between ISR execution and background task logic.

### 3. Dual-LED Steady Light Mode (Trigger K0)
* Pressing the secondary external button shifts the FSM into a **steady-state (continuous light)** mode on the dual LEDs. Mutual exclusion between states is strictly managed to ensure predictable GPIO output behavior.

### 4. Low-Power & Standby Management (Stop Mode)
* **Power Optimization:** Upon requesting standby, the ARM Cortex-M core enters **Stop Mode** via the `__WFI()` (Wait For Interrupt) instruction, shutting down core system clocks and drastically minimizing power consumption.
* **Wake-up & Pending Interrupt Sanitization:** Prior to sleep entry, the firmware proactively clears pending EXTI flags (`__HAL_GPIO_EXTI_CLEAR_IT`) to prevent spurious wake-ups caused by electrical transients or mechanical contact bounce (*glitches*).
* **Clock Restoration:** Upon waking from an external event, the system immediately restores the PLL and system clock configuration via `SystemClock_Config()`.
* **Post-Wake Logic:** If an active interrupt or pending request remains upon waking, the system immediately services the associated state action; if no pending interrupts exist, execution smoothly resumes from the default standard blinking state.

### 5. Fault Tolerance & Custom Error Handler
* In the event of critical faults or hardware verification failures, the firmware invokes a customized `Error_Handler()` routine.
* **Visual Diagnostic Flicker:** The function globally disables interrupts (`__disable_irq()`) and traps execution inside an infinite safety loop (`while(1)`), driving a **high-frequency visual flicker** on the diagnostic LED to physically signal a critical controller fault to the operator.

## Technology Stack
* **MCU:** STM32 Family (compatible with STM32F4 / Cortex-M).
* **Language:** C (Bare-metal).
* **Toolchain:** STM32CubeIDE / CubeMX.
  
## Video Demos & Functional Testing

### 1. Core Functionality 

https://github.com/user-attachments/assets/52b48ae7-9a90-4be7-820a-6e83079b9a99


### 2. Low-Power & Stop Mode Management


https://github.com/user-attachments/assets/093c3859-74e4-4fa6-afed-8e884a922466



### 3. Error Handler 

https://github.com/user-attachments/assets/76168107-6902-41b7-9138-8794068ad21a











