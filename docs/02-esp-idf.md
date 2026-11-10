# 02 - ESP-IDF Fundamentals

## 1. Framework Overview

ESP-IDF (Espressif IoT Development Framework) is the official
software development framework for Espressif SoCs.

It provides the libraries, drivers, operating system integration,
build tools, and configuration mechanisms required to develop
embedded applications.

### 1.1. Software Architecture

An ESP-IDF application consists of several software layers:

| Layer | Responsibility |
|---|---|
| Application | Application-specific logic and functionality |
| Middleware | Networking protocols, storage, security, and services |
| Drivers | Hardware peripheral control |
| FreeRTOS | Task scheduling and synchronization |
| HAL / LL | Hardware abstraction and low-level register access |
| Hardware | ESP32 CPU, memory, peripherals, and wireless interfaces |

These layers represent a conceptual organization rather than
strictly isolated software modules.

ESP-IDF applications use framework APIs to interact with hardware
and system services without implementing every low-level operation.

### 1.2. Component-Based Architecture

ESP-IDF organizes software into components.

A component is a collection of source files, headers, configuration,
and build definitions that provides specific functionality.

Common ESP-IDF components include:

| Component | Purpose |
|---|---|
| `freertos` | Task scheduling and synchronization |
| `esp_driver_gpio` | GPIO control |
| `esp_wifi` | Wi-Fi connectivity |
| `esp_netif` | Network interface management |
| `esp_event` | Event handling |
| `esp_http_client` | HTTP/HTTPS client |
| `nvs_flash` | Non-volatile key-value storage |
| `esp_timer` | High-resolution software timers |
| `esp_system` | System-level functions |

Applications can also define their own components.

Components declare dependencies on other components, allowing
the build system to determine the required libraries and headers.

### 1.3. Build and Configuration Tools

ESP-IDF uses several tools to configure, compile, and deploy
applications.

| Tool | Purpose |
|---|---|
| `idf.py` | Main command-line interface |
| CMake | Project and component build configuration |
| Ninja | Build execution |
| Kconfig | Configuration option definitions |
| `menuconfig` | Interactive project configuration |
| `esptool` | Firmware flashing and chip communication |
| IDF Component Manager | External component dependency management |

The `idf.py` command provides a unified interface to these tools.

Typical operations include:

- Configuring the target SoC.
- Selecting project configuration options.
- Building application binaries.
- Flashing firmware.
- Monitoring serial output.

ESP-IDF supports command-line development independently
of any specific editor or IDE.

## References

- [ESP-IDF Programming Guide](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/)
- [ESP-IDF Build System](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-guides/build-system.html)
- [ESP-IDF Get Started](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/)

---

## 2. ESP-IDF and FreeRTOS

### 2.1. FreeRTOS Integration

FreeRTOS is a real-time operating system (RTOS) kernel integrated
into ESP-IDF.

It provides:

- Task creation and scheduling.
- Priority-based preemption.
- Inter-task communication.
- Synchronization mechanisms.
- Software timers.
- Memory management facilities.

ESP-IDF uses FreeRTOS to execute application tasks and internal
system services.

Unlike a bare-metal application, execution is managed by a
scheduler that determines which task runs on each CPU core.

### 2.2. Dual-Core Support

The classic ESP32 contains two CPU cores:

- Core 0 (PRO_CPU).
- Core 1 (APP_CPU).

ESP-IDF provides a modified FreeRTOS implementation with
symmetric multiprocessing (SMP) support.

Tasks can be:

- Pinned to Core 0.
- Pinned to Core 1.
- Unpinned, allowing execution on either core.

Each core schedules eligible tasks according to their priorities.

A task cannot execute simultaneously on both cores.

Shared resources require proper synchronization to prevent
race conditions and data corruption.

### 2.3. Application Entry Point

ESP-IDF applications use `app_main()` as their entry point.

Unlike a traditional C application, `main()` is not implemented
by the application.

ESP-IDF performs system initialization, starts the FreeRTOS
scheduler, and executes `app_main()` from a system-created task.

Minimal example:

```c
void app_main(void)
{
    // Application initialization
}
```

The application does not need to call `vTaskStartScheduler()`.

The `app_main()` function can create additional tasks and return,
or continue executing as the main application task.

If `app_main()` returns, its FreeRTOS task is deleted.
Other tasks continue running normally.

### 2.4. Tasks and Execution

A FreeRTOS task is an independent execution context managed
by the scheduler.

Each task has:

- Its own stack.
- A priority.
- An execution state.
- A task control block (TCB).
- An optional CPU core affinity.

Common task states include:

| State | Description |
|---|---|
| Running | Currently executing on a CPU core |
| Ready | Able to execute, waiting for CPU time |
| Blocked | Waiting for an event or timeout |
| Suspended | Explicitly excluded from scheduling |

A blocked task does not consume CPU time while waiting.

Tasks should generally block when they have no work to perform,
rather than continuously polling for events.

### 2.5. ESP-IDF FreeRTOS Differences

ESP-IDF FreeRTOS differs from the original FreeRTOS kernel
in several aspects:

- Support for dual-core scheduling.
- CPU affinity for tasks.
- SMP-aware synchronization.
- Task stack sizes specified in bytes rather than words.
- Automatic scheduler initialization.

ESP-IDF also creates internal tasks for system services,
including timer processing and other framework components.

Application design must account for these tasks and their
resource requirements.

Detailed task scheduling and synchronization mechanisms
are covered in the FreeRTOS practical section.
```

## References:

- [FreeRTOS Overview](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/system/freertos.html)
- [ESP-IDF FreeRTOS](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/system/freertos_idf.html)
- [Application Startup Flow](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-guides/startup.html)

---
