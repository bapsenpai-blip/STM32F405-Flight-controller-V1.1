# STM32F405-Flight-controller-V1.1
After the freaking horrible v1, I have changed a lot to improve my FC as much as possible.
<img width="930" height="930" alt="RealFCv1 1" src="https://github.com/user-attachments/assets/4b17d8c7-d8d8-4c7b-a31a-9db40b86a533" />
<img width="930" height="930" alt="RealFCv1 1-back" src="https://github.com/user-attachments/assets/9f82de0e-71c9-4453-a43d-ebbe5515305b" />


### Changes from the V1
- Remove the SD card slot (took too much space). Using a blackbox instead.
- Remove 1 USB-C connector for ESP32. Now using an usb-mux + slide switch to choose which MCU is connected.
- Cleaner layout, board size changed from freaking big to 50x50mm (30.5x30.5 mounting hole).

### Components
- STM32F405RGT6: doing the basic things of an FC (calculate angles, PID, motor mixer,...)
- ESP32 S3 - WROOM 02: using DroneBrigde method for affordable Telemetry (I have no money to buy an $60 MAVLink Telemetry module)
- 8MHz Crystals from China: 10pF CLoad
- IMU: ICM-20602
- Barometer: BMP280
- Flash Memory: W25Q128JVS (16MB SPI Flash for Blackbox logging)
- USB-C Receptable 2.0 16 pins
- FSUSB42MUX: combining with a slideswitch to change the USB signal to desired MCU.
- Power supply: I'm gonna use an external Buck Converter, because I have no idea about power electronics
- USBLC6-2SC6: ESD protection for USB-C
- 2 AP2112K-3.3Vs: one for STM32 and other main components, one for only ESP32 (ESP32 consumes a lot of current when using Wi-fi/BLE)
- 2 SS14 Diodes: preventing reverse currents
- Resistors and capacitors: based on the recommendation of components' datasheets.

### Pin Mapping & Peripheral Connections

| Peripheral / Interface | Signal Name | MCU Pin | Description / Target |
| :--- | :--- | :--- | :--- |
| **IMU (ICM-20602)** | SPI2_NSS / SCK / MISO / MOSI | `PB12` / `PB13` / `PB14` / `PB15` | SPI Sensor Bus |
| | IMU_DDRY | `PC6` | EXTI Data Ready Interrupt |
| **Blackbox (W25Q128)** | SPI1_NSS / SCK / MISO / MOSI | `PA4` / `PA5` / `PA6` / `PA7` | 16MB SPI NOR Flash |
| **Barometer (BMP280)** | I2C1_SCL / I2C1_SDA | `PB8` / `PB9` | Internal Baro (4.7kΩ pull-ups) |
| **Motors (DSHOT)** | DSHOT_CH1 / CH2 | `PB4` / `PB5` | TIM3_CH1, TIM3_CH2 (to J3 ESC) |
| | DSHOT_CH3 / CH4 | `PB6` / `PB7` | TIM4_CH1, TIM4_CH4 (to J3 ESC) |
| **Telemetry (ESP32-S3)** | UART3_TX / UART3_RX | `PB10` / `PB11` | DroneBridge Telemetry link |
| **RC Receiver (J4)** | UART1_TX / UART1_RX | `PA9` / `PA10` | Serial RX (CRSF / SBUS / IBUS) |
| **GPS / Ext Sensor (J5)** | UART2_TX / UART2_RX | `PA2` / `PA3` | External GPS Serial Port |
| | I2C1_SCL / I2C1_SDA | `PB8` / `PB9` | External Compass / Sensor Bus |
| **ESC Telemetry (J3)** | UART4_RX | `PA1` | ESC Telemetry feedback |
| **Current / Voltage Sensing**| ADC1_IN14 (CURR) | `PC4` | Current sensor via 1kΩ / 100nF filter |
| | ADC1_IN15 (VBAT) | `PC5` | Battery voltage via 10:1 divider (11x) |
| **Status LEDs** | STAT_LED1 / LED2 / LED3 | `PC14` / `PB3` / `PC13` | Debug & status indicators |
| **USB & Debug (J7)** | S_D- / S_D+ | `PA11` / `PA12` | USB FS (via FSUSB42 MUX) |
| | SWDIO / SWCLK | `PA13` / `PA14` | SWD Debug port |

### Status & Firmware
- [x] Hardware verification: Validated on a custom STM32F405 breakout board (SPI, I2C, UART peripherals verified and working stably).
- [x] V1.1 PCB design & layout completed (ready for manufacturing/bring-up).
- [ ] Bring-up V1.1 integrated board (test power rails, USB mux swiSching, and onboard sensors).
- [ ] Port and configure open-source flight firmware (Betaflight / INAV / ArduPilot target).
- [ ] Bring-up ESP32-CS DroneBridge telemetry link.
- [ ] Bench test with 4-in-1 ESC (DShot) & maiden flight test.

**Firmware & Tools:** 
- Open-source flight controller firmware (target bring-up)
- ESP-IDF / DroneBridge for ESP32-S3
- VS Code - PlatformIO for low-level validation & debugging
