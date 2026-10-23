# B-G431B-ESC1-Brushled-DC-PWM-Test
B-G431B-ESC1 Brushed DC Motor – Potentiometer-Controlled PWM

This project reads the on-board potentiometer of the STMicroelectronics B-G431B-ESC1 board and uses it to change the PWM duty cycle that drives a brushed DC motor. It is an HAL-based CMake project generated with STM32CubeMX and built with VS Code (STM32 extension).

Note: The board is designed for three-phase brushless (BLDC/PMSM) motors. In this project, two of the three half-bridges are used to form an H-bridge, and the brushed motor is connected between two phase outputs. The ST Motor Control SDK (MCSDK) is not used.

How it works
ADC1 reads the on-board potentiometer (12-bit, 0–4095).
The reading is scaled against the TIM1 auto-reload value (ARR) to get a CCR value.
The CCR value is written to the PWM channel of phase U (duty cycle = motor speed).
Phase V is held LOW (low-side MOSFET on), so the motor is driven between U and V.
Duty cycle is limited to 95 % so the high-side MOSFET's bootstrap capacitor can recharge.
Hardware
Part	Description
Board	B-G431B-ESC1 Discovery kit
MCU	STM32G431CB
Gate driver / MOSFETs	L6387 / STL180N6F7
Motor	Brushed DC motor (between the U and V outputs)
Supply	A current-limited bench supply is recommended
Wiring
Connect one motor terminal to OUT_U and the other to OUT_V. Phase W is unused.
The solder bridges on the board must be set for brushed motor operation according to the board's user manual.
Pins (verify against the pinout table in the board manual)
Function	Pin
Phase U PWM (high / low side)	PA8 (TIM1_CH1) / PC13 (TIM1_CH1N)
Phase V PWM (high / low side)	PA9 (TIM1_CH2) / PA12 (TIM1_CH2N)
Potentiometer	PB12 (ADC1_IN11)
Software configuration (CubeMX)
MCU: STM32G431CB, Debug: Serial Wire
TIM1: CH1/CH1N and CH2/CH2N as PWM, around 20 kHz, dead-time enabled (roughly 500 ns – 1 µs)
ADC1: only the potentiometer channel (IN11), single-ended, 12-bit
Toolchain: CMake
Build and flash
Open the folder in VS Code (the STM32CubeIDE for Visual Studio Code extension must be installed).
Build from the extension, or from a terminal:
   cmake --preset Debug
   cmake --build build/Debug
Connect the board's ST-LINK over USB and flash it with Debug (F5).
Monitoring values

While debugging, add these global variables to Live Watch (or Live Expressions in CubeIDE):

adc_val – raw ADC value (0–4095)
ccr_val – PWM compare value
duty_pct – duty cycle (%)
Safety notes
Do the first test at low voltage (e.g. 12 V) with a current-limited supply.
Start with the potentiometer at zero so the motor does not start abruptly.
Do not skip dead-time; otherwise the high-side and low-side MOSFETs can conduct at the same time and cause a shoot-through.
Check the pin assignments and solder bridges against the official user manual (UM2516).
Possible improvements
Treat the potentiometer's midpoint as zero for bidirectional rotation
ADC averaging and duty-cycle ramping
Current measurement and current limiting
Sending data to the PC over UART
License
