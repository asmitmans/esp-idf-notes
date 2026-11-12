# 03 - Development Environment

## 1. Environment Overview

The development environment uses the official Espressif toolchain
and a command-line workflow.

### 1.1. Toolchain

| Tool | Purpose |
|---|---|
| ESP-IDF v6.1 | Framework and SDK |
| Xtensa GCC | C/C++ cross-compiler for ESP32 |
| CMake | Build configuration |
| Ninja | Build execution |
| Python | ESP-IDF scripts and utilities |
| idf.py | Project management, build, flash, and monitor |
| esptool | Flash programming and chip communication |
| OpenOCD | JTAG debugging |

The development environment runs on Ubuntu Linux.

Visual Studio Code is used for source editing, code navigation,
and development assistance.

Building, flashing, monitoring, and Git operations are performed
primarily from the terminal.

### 1.2. Version Management

ESP-IDF version: v6.1

The framework version must remain consistent across development
environments and CI pipelines.

ESP-IDF and its toolchain are installed outside the project
repository.

The project repository contains application sources,
configuration, documentation, and examples.

### 1.3. Installation Method

Espressif provides the ESP-IDF Installation Manager (EIM)
for installing and managing ESP-IDF versions and development tools.

EIM supports graphical and command-line operation.

The command-line installation method is preferred to maintain
a terminal-based development workflow.

## References

- [ESP-IDF v6.1 Release](https://github.com/espressif/esp-idf/releases/tag/v6.1)
- [ESP-IDF Get Started](https://docs.espressif.com/projects/esp-idf/en/v6.1/esp32/get-started/)

---

## 2. Installation on Ubuntu Linux

### 2.1. Prerequisites

ESP-IDF requires a supported Python installation and development
tools for compiling and flashing firmware.

ESP-IDF v6.1 requires Python 3.10 or newer.

The ESP-IDF Installation Manager (EIM) can install and manage
the required development tools.

When EIM is installed through APT, the installation procedure
handles its required system dependencies.

### 2.2. Install ESP-IDF Installation Manager

Add Espressif's official APT repository:

```bash
echo "deb [trusted=yes] https://dl.espressif.com/dl/eim/apt/ stable main" | sudo tee /etc/apt/sources.list.d/espressif.list
sudo apt update
```

Install the command-line version:

```bash
sudo apt install eim-cli
```

Verify the installation:

```bash
eim --help
```

### 2.3. Install ESP-IDF v6.1

Install the selected ESP-IDF version and its development tools:

```bash
eim install -i v6.1
```

The explicit version prevents the installation from automatically
selecting a different ESP-IDF release.

EIM installs the framework and associated development tools
outside the application repository.

### 2.4. Activate the Environment

ESP-IDF requires an activated development environment before
running its command-line tools.

After installation, EIM prints the path to an activation script.

Activate the environment using the command provided by EIM:

```bash
source <path-to-activation-script>
```

The activation script configures the current shell with the
environment variables and executable paths required by ESP-IDF.

Activation must be repeated in each new terminal session.

### 2.5. Verify the Installation

Check the installed ESP-IDF version:

```bash
idf.py --version
```

Expected version:

```text
ESP-IDF v6.1
```

Check the framework installation path:

```bash
echo "$IDF_PATH"
```

The `IDF_PATH` variable identifies the active ESP-IDF installation.

## References

- [ESP-IDF Installation on Linux](https://docs.espressif.com/projects/esp-idf/en/v6.1/esp32/get-started/linux-setup.html)
- [ESP-IDF Command-Line Development](https://docs.espressif.com/projects/esp-idf/en/v6.1/esp32/get-started/linux-macos-start-project.html)

---

## 3. First Project: Hello World

The official `hello_world` example verifies the ESP-IDF development
environment, build system, flashing process, and serial communication.

### 3.1. Prepare the Project

Activate the ESP-IDF v6.1 environment.

Create a directory for development projects:

```bash
mkdir -p ~/esp
cd ~/esp
```

Copy the official example:

```bash
cp -r "$IDF_PATH/examples/get-started/hello_world" .
cd hello_world
```

The example includes the required source code, CMake files,
and project configuration.

ESP-IDF project paths must not contain spaces.

### 3.2. Configure the Target

Select the classic ESP32:

```bash
idf.py set-target esp32
```

ESP-IDF targets identify the SoC, not a specific development board.

For example, development boards using ESP32-WROOM-32E or
ESP32-WROVER modules share the `esp32` target.

This differs from Zephyr, which uses board definitions that
describe specific hardware configurations.

ESP-IDF primarily configures the SoC through Kconfig and `sdkconfig`.
Board-specific hardware, such as GPIO assignments and external
peripherals, must be handled by the application or its components.

List the supported targets:

```bash
idf.py --list-targets
```

Check the currently configured target:

```bash
grep CONFIG_IDF_TARGET sdkconfig
```

Open the configuration menu:

```bash
idf.py menuconfig
```

The default configuration is sufficient for this example.

### 3.3. Build the Application

Compile the project:

```bash
idf.py build
```

The build system:

1. Configures the project using CMake.
2. Resolves component dependencies.
3. Compiles the required components.
4. Links the application.
5. Generates the bootloader, partition table, and firmware binaries.

Generated files are stored in the `build/` directory.

A successful build ends with:

```text
Project build complete.
```

### 3.4. Flash the Firmware

Connect the ESP32 development board through USB.

Identify the serial device:

```bash
ls /dev/ttyUSB* /dev/ttyACM* 2>/dev/null
```

Typical Linux serial devices include `/dev/ttyUSB0` and
`/dev/ttyACM0`.

Flash the firmware using the detected port:

```bash
idf.py -p /dev/ttyUSB0 flash
```

Replace `/dev/ttyUSB0` with the actual device.

The `flash` command automatically builds the project if needed.

### 3.5. Monitor the Application

Start the serial monitor:

```bash
idf.py -p /dev/ttyUSB0 monitor
```

After startup, the application prints:

```text
Hello world!
```

The example also displays chip information, available heap
memory, and a restart countdown.

Exit the monitor using `Ctrl+]`.

Building, flashing, and monitoring can be combined:

```bash
idf.py -p /dev/ttyUSB0 flash monitor
```

### 3.6. Verification

The development environment is operational when:

- `idf.py` recognizes ESP-IDF v6.1.
- The project builds successfully.
- The ESP32 is detected through its serial interface.
- Firmware flashing completes without errors.
- The serial monitor displays the expected application output.

## References

[ESP-IDF v6.1 — Start a Project on Linux and macOS](https://docs.espressif.com/projects/esp-idf/en/v6.1/esp32/get-started/linux-macos-start-project.html?utm_source=chatgpt.com).

---
