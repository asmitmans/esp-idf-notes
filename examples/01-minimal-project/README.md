# 01 - Minimal ESP-IDF Project

## Objective

Create and build a minimal ESP-IDF application from scratch.

This example demonstrates:

- Basic ESP-IDF project structure.
- CMake project initialization.
- Component registration.
- Application entry point (`app_main`).
- Target configuration and firmware compilation.

**Target:** ESP32
**Framework:** ESP-IDF v6.1
**Language:** C

## 1. Project Structure

Create the following directory structure:

```text
01-minimal-project/
├── README.md
├── CMakeLists.txt
└── main/
    ├── CMakeLists.txt
    └── main.c
```

The project contains a single application component: `main`.

## 2. Project Configuration

### 2.1. Root CMakeLists.txt

The root `CMakeLists.txt` initializes the ESP-IDF build system
and defines the project name.

```cmake
cmake_minimum_required(VERSION 3.22)

include($ENV{IDF_PATH}/tools/cmake/project.cmake)

project(minimal_project)
```

### 2.2. Main Component

The `main/CMakeLists.txt` file registers the application source:

```cmake
idf_component_register(
    SRCS "main.c"
    INCLUDE_DIRS "."
)
```

The `main` component is automatically discovered by the
ESP-IDF build system.

## 3. Application Code

Implement the application entry point in `main/main.c`:

```c
#include <stdio.h>

void app_main(void)
{
    printf("Hello from ESP-IDF!\n");
}
```

ESP-IDF initializes the system and starts the FreeRTOS
scheduler before executing `app_main()`.

The function runs within a FreeRTOS task created by ESP-IDF.

## 4. Build Procedure

### 4.1. Activate ESP-IDF

Activate the ESP-IDF v6.1 environment using the activation
script provided during installation.

Verify the active version:

```bash
idf.py --version
```

### 4.2. Select the Target

From the project directory:

```bash
idf.py set-target esp32
```

This selects the classic ESP32 SoC.

### 4.3. Build

Compile the application:

```bash
idf.py build
```

The build system generates:

- Application executable (`minimal_project.elf`).
- Application firmware (`minimal_project.bin`).
- Bootloader binary.
- Partition table binary.

Generated artifacts are stored in `build/`.

### 4.4. Verify Build Artifacts

Check the application binaries:

```bash
ls -lh build/minimal_project.elf build/minimal_project.bin
```

A successful build confirms that the project structure,
component registration, and application code are valid.

## 5. Hardware Execution

Connect an ESP32 development board and identify its serial port.

Flash and monitor the application:

```bash
idf.py -p /dev/ttyUSB0 flash monitor
```

Replace `/dev/ttyUSB0` with the actual serial device.

Expected application output:

```text
Hello from ESP-IDF!
```

Exit the serial monitor using `Ctrl+]`.

## 6. Verification

The example is considered successfully built when:

- The ESP32 target is configured.
- CMake discovers the `main` component.
- Compilation and linking complete without errors.
- Application, bootloader, and partition table binaries are generated.

Hardware execution additionally verifies firmware flashing
and serial output.

## References

- [ESP-IDF Build System](https://docs.espressif.com/projects/esp-idf/en/v6.1/esp32/api-guides/build-system.html)
- [ESP-IDF Getting Started](https://docs.espressif.com/projects/esp-idf/en/v6.1/esp32/get-started/linux-macos-start-project.html)
- [ESP-IDF Application Startup Flow](https://docs.espressif.com/projects/esp-idf/en/v6.1/esp32/api-guides/startup.html)