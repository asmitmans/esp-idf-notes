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
