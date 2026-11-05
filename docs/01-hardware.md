# 01 - ESP32 Hardware

## 1. SoC, Module, and Development Board

ESP32-based hardware can be organized into three levels:

| Level | Example | Description |
|---|---|---|
| SoC | ESP32 | Integrated circuit containing processors, memory, peripherals, and wireless connectivity. |
| Module | ESP32-WROOM-32E-N4 | Integrates the SoC, external Flash, crystal oscillator, antenna, and supporting components. |
| Development board | ESP32 DevKit | Provides power regulation, programming interfaces, and access to GPIO pins. |

### 1.1. SoC (System on Chip)

The ESP32 is a microcontroller SoC that integrates:

- Xtensa LX6 processors.
- ROM and SRAM.
- External memory controllers.
- Digital and analog peripherals.
- Wi-Fi and Bluetooth radios.
- Clock, power management, and reset circuitry.

Additional external components are required to build a functional system.

### 1.2. ESP32-WROOM-32E-N4 Module

The module integrates:

- ESP32 SoC.
- 4 MB external SPI Flash.
- 40 MHz crystal oscillator.
- Integrated PCB antenna.
- RF matching and decoupling components.
- Metal shielding.

The N4 designation identifies the 4 MB Flash variant.

The module simplifies PCB integration by providing the external
memory and RF circuitry required by the ESP32.

### 1.3. Development Board

A typical ESP32 development board includes:

- ESP32-WROOM module.
- Voltage regulator.
- USB-to-UART bridge.
- Reset and programming circuitry.
- BOOT and EN buttons.
- GPIO and power headers.

The USB-to-UART bridge provides a serial interface for firmware
flashing and communication.

The BOOT button can be used to select the firmware download mode.
The EN button resets the SoC.

Pin assignments and supporting circuits depend on the board
manufacturer and model.

Espressif's ESP32-DevKitC V4 is an official reference board.
Other 38-pin development boards may use different schematics.

## References

- [ESP32 Datasheet](https://documentation.espressif.com/esp32_datasheet_en.html)
- [ESP32-WROOM-32E Datasheet](https://documentation.espressif.com/esp32-wroom-32e_esp32-wroom-32ue_datasheet_en.html)
- [ESP32-DevKitC V4 User Guide](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32/esp32-devkitc/user_guide.html)

---

## 2. CPU, Memory, and Peripherals

### 2.1. CPU

The ESP32-WROOM-32E uses an ESP32 SoC with two Xtensa LX6
32-bit processor cores.

Main characteristics:

- Dual-core architecture.
- Maximum CPU frequency: 240 MHz.
- Hardware floating-point support.
- Hardware interrupt controller.
- Support for JTAG debugging.

Both cores can execute application code. ESP-IDF uses FreeRTOS
to schedule tasks and manage concurrent execution.

### 2.2. Memory

The ESP32 contains several types of memory with different purposes.

| Memory | Size | Purpose |
|---|---|---|
| ROM | 448 KB | Boot code and built-in functions |
| SRAM | 520 KB | Instructions and runtime data |
| RTC FAST SRAM | 8 KB | Memory associated with the RTC subsystem |
| RTC SLOW SRAM | 8 KB | Memory associated with the RTC subsystem |
| External SPI Flash | 4 MB | Firmware and persistent data |

The ESP32-WROOM-32E-N4 does not include external PSRAM.

#### Internal SRAM

ESP-IDF distinguishes between different memory regions:

- **IRAM:** Internal RAM used for executable instructions.
- **DRAM:** Internal RAM used for application data.
- **D/IRAM:** Internal RAM that can be used for instructions or data.

The 520 KB of internal SRAM cannot be treated as a single
contiguous region available to the application.

Available memory depends on the memory layout, system components,
and runtime allocations.

#### External Flash

The external SPI Flash stores:

- Second-stage bootloader.
- Partition table.
- Application firmware.
- Persistent data partitions.

Most application instructions execute directly from mapped
Flash through the memory management unit (MMU) and cache.

Code that requires execution while the Flash cache is unavailable
must reside in internal instruction memory (IRAM).

Flash is non-volatile, while ordinary SRAM loses its contents
when power is removed.

### 2.3. Main Peripherals

The ESP32 integrates multiple hardware peripherals.

| Peripheral | Typical application |
|---|---|
| GPIO | Digital inputs and outputs |
| UART | Serial communication and debugging |
| SPI | Communication with external devices |
| I2C | Communication with sensors and peripherals |
| ADC | Analog signal acquisition |
| DAC | Analog signal generation |
| LEDC | PWM signal generation |
| GPTimer | Hardware timing |
| RMT | Transmission and reception of timed digital signals |
| TWAI | CAN-compatible communication |
| I2S | Digital audio interfaces |
| Watchdog timers | Detection of execution failures |

The availability of peripheral signals on external pins depends
on the SoC pin configuration and module connections.

### 2.4. Wireless Connectivity

The ESP32 integrates:

- 2.4 GHz Wi-Fi (IEEE 802.11 b/g/n).
- Bluetooth Classic (BR/EDR).
- Bluetooth Low Energy (BLE), version 4.2.

Wi-Fi supports Station (STA) and SoftAP operating modes.

Wireless functionality is managed through dedicated hardware
and software components provided by ESP-IDF.

## References
- [ESP-IDF Memory Types](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-guides/memory-types.html)
- [ESP-IDF Heap Memory Allocation](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/system/mem_alloc.html)

---

## 3. GPIO and Pin Restrictions

### 3.1. General-Purpose I/O

The ESP32 provides 34 physical GPIO pins.

GPIOs can be configured for different functions:

- Digital input.
- Digital output.
- Interrupt generation.
- Peripheral signal routing.
- Analog functions on supported pins.

The GPIO matrix allows many peripheral signals to be routed
to different pins, reducing dependency on fixed pin assignments.

Not all GPIOs support the same functions, and not all SoC pins
are exposed by the ESP32-WROOM-32E module.

### 3.2. Pin Restrictions

Some GPIOs have hardware restrictions or special functions.

| GPIO | Restriction or special function |
|---|---|
| GPIO6-11 | Connected to external SPI Flash; unavailable for general use |
| GPIO34-39 | Input only; no internal pull-up or pull-down resistors |
| GPIO0, 2, 5, 12, 15 | Strapping pins used during boot |
| GPIO1, 3 | UART0, typically used for flashing and serial logging |
| GPIO12-15 | Default JTAG interface pins |

GPIO37 and GPIO38 are not exposed by the ESP32-WROOM-32E module.

#### Strapping Pins

Strapping pins determine specific hardware configurations
during startup.

Their logic levels are sampled during reset and influence
parameters such as boot mode and Flash voltage configuration.

External circuits connected to these pins must not interfere
with the required startup levels.

GPIO0 is particularly important:

- HIGH during reset: normal boot from Flash.
- LOW during reset: serial download mode, subject to other
  boot configuration requirements.

GPIO12 also requires special consideration because it can
select the voltage supplied to the SPI Flash.

### 3.3. Electrical Characteristics

ESP32 GPIOs operate in the 3.3 V logic domain.

Important considerations:

- GPIOs are not 5 V tolerant.
- External circuits must respect the documented voltage limits.
- GPIO output drive capability is limited.
- High-current loads require external driver circuits.
- Inputs must not be left floating when a defined logic level
  is required.

Internal pull-up and pull-down resistors are available on
supported GPIOs.

External resistors may be required depending on the circuit
and startup requirements.

### 3.4. Analog Input Limitations

The ESP32 includes two SAR ADC units:

- ADC1.
- ADC2.

ADC2 is shared with the Wi-Fi subsystem.

On the classic ESP32, ADC2 access is restricted while
Wi-Fi is active.

ADC1 is generally the appropriate choice for analog
measurements that must operate concurrently with Wi-Fi.

ADC accuracy, attenuation, calibration, and electrical
limitations must be considered when designing analog inputs.

### 3.5. Pin Selection

GPIO selection must account for:

1. Required input or output functionality.
2. Boot strapping requirements.
3. Internal Flash connections.
4. Peripheral pin assignments.
5. Electrical characteristics.
6. Development board circuitry.

The SoC datasheet, module datasheet, and development board
schematic must be checked before assigning pins.

## References
- [ESP-IDF GPIO & RTC GPIO](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/peripherals/gpio.html)
- [ESP32 Technical Reference Manual](https://documentation.espressif.com/esp32_technical_reference_manual_en.pdf)

---


