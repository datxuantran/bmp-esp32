# Tutorial: Using an ESP32‑WROOM (NodeMCU) as a Wi‑Fi Debug Probe for an STM32F446RE

This guide explains how to configure an ESP32‑WROOM (NodeMCU) module to act as a networked debug probe for the STM32F446RE microcontroller over a Wi‑Fi connection.

## Prerequisites

- A Linux computer
- An ESP32‑WROOM (NodeMCU) development board
- An STM32F446RE development board
- USB cables for programming both boards

## 1. Install ESP‑IDF v5.0.8

1. Clone the ESP‑IDF v5.0.8 release:

   ```bash
   git clone -b v5.0.8 --recursive https://github.com/espressif/esp-idf.git esp-idf-v5.0.8
   cd esp-idf-v5.0.8/

   ```

2. Follow the setup instructions for your operating system:

   - For Linux (and macOS), see the [Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/v5.0.8/esp32/get-started/linux-macos-setup.html).
   - Run the install script and source the environment:

     ```bash
     ./install.sh
     . ./export.sh

     ```

## 2. Clone the Black Magic Probe Firmware

This firmware port enables Black Magic Probe functionality on the ESP32.

```bash
git clone --recursive https://github.com/datxuantran/bmp-esp32.git
cd bmp-esp32

```

## 3. Build and Flash the ESP32 Firmware

1. Set up the build environment for the ESP32 target:

   ```bash
   idf.py set-target esp32
   idf.py menuconfig

   ```

2. In the menuconfig, configure the following options:

   - **Flash Size**: 4MB
   - **Partition Table**: Custom (using `partition-table.csv`)
   - **FreeRTOS**: Enable the trace facility

   [Firmware Build Configuration Tutorial](https://drive.google.com/file/d/1NdejMxhQbbLoIZpreeCptuE0EwOAQF8q/view?usp=drive_link)

3. Build and flash the firmware:

   ```bash
   idf.py build
   idf.py -p /dev/ttyUSB0 flash

   ```

   Replace `/dev/ttyUSB0` with the serial port for your ESP32.

## 4. Wire the ESP32 to the STM32F446RE

To enable SWD debugging over Wi-Fi, connect the ESP32’s SWD lines to the STM32F446RE as follows:

| -------- | ESP32 WROOM (NodeMCU) | STM32F446RE Board |
| -------- | --------------------- | ----------------- |
| SWCLK    | GPIO25                | CN7 Pin 15 (PA14) |
| SWDIO    | GPIO26                | CN7 Pin 13 (PA13) |

> **Note:** Always tie the grounds of both boards together.

[STM32F446RE Pinout](https://drive.google.com/file/d/1cwwmIsM9unlFX2JTwDeKmQf6_imgN2uE/view?usp=drive_link)

[ESP32–STM32 Wiring Diagram](https://drive.google.com/file/d/1Mdbb5vDrWbj8sRMztsX0WrzNKjCeUqJm/view?usp=drive_link)

> The wiring diagram above is illustrative; refer to the pin mappings listed in the table for precise connections.

## 5. Connect to the ESP32 via Wi‑Fi

After the ESP32 reboots, open your computer’s Wi‑Fi settings. A network named **blackmagic** will appear. Connect to this network using the password **iamwitcher**.

## 6. Debugging the STM32F446RE

### 6.1 Flashing a Simple Program

1. Write a simple `main.c` for the STM32 board (e.g., toggling an LED).
2. Build and flash the program onto STM32 board with STM32CubeIDE

### 6.2 Start a GDB Session Over Wi‑Fi

1. Open `arm-none-eabi-gdb` with your ELF file:

   ```bash
   arm-none-eabi-gdb Debug/your_project.elf

   ```

2. In the GDB console, connect to the ESP32 Black Magic probe:

   ```gdb
   target extended-remote tcp:192.168.4.1:2345
   monitor swdp_scan
   att 1
   ```

3. Use normal GDB commands (`finish`, `next`, `break`, `step`, `continue`, etc. ) to debug.

[Debug session in terminal example](https://drive.google.com/file/d/1iw3BH6VvlD1ol4ep_LglJgIOgjLaTW2s/view?usp=drive_link)

### 6.3 Using STM32CubeIDE

List of interactive tutorial on configure the gdb hardware debugging and debug session in stm32cubeIDE
[Debug session in STM32CubeIDE](https://drive.google.com/file/d/1lYrRjPGaxeAm9QpiFlFEkb3eyjNCJ3ur/view?usp=drive_link)
[GDB Hardware Debugging_Main_Configuration](https://drive.google.com/file/d/1KYCKNr1jPEVpw2qq8PpECrbc_k2X_3TE/view?usp=sharing)
[GDB Hardware Debugging_Debugger_Configuration](https://drive.google.com/file/d/1bes3LA_oOc4WGDeuwI9wwz-3gZbU3R1X/view?usp=drive_link)
[GDB Hardware Debugging_Startup_Configuration](https://drive.google.com/file/d/1wdwB2Liqvi05xLNEKN0Tlf1TVIoUkDpR/view?usp=drive_link)

Text tutorial on how to configure the GDB hardware debugging

1. Create or import your STM32 project in STM32CubeIDE.
2. In **Run → Debug Configurations**, add a new **GDB Hardware Debugging** configuration.
3. Set the **Debugger** to:
   - GDB command `arm-none-eabi-gdb`
   - Remote Target/Debug Server `Black Magic Probe`
   - Remote Target/Protocol chooses `extended-remote`
   - Remote Target/Connection chooses `192.168.4.1:2345`
4. Set the **Startup**:

- Initialization Commands

```
set logging on
set mem inaccessible-by-default off
monitor swdp_scan
attach 1
```

Refer to [Black Magic Probe STM32CubeIDE](https://black-magic.org/usage/stm32cubeide.html)

5. Apply and click **Debug**. STM32CubeIDE will connect over Wi‑Fi to the Black Magic probe.

### 6.3 Using STM32CubeIDE

Below are interactive tutorials for configuring GDB hardware debugging and starting a debug session in STM32CubeIDE:  
[Debug session in STM32CubeIDE](https://drive.google.com/file/d/1lYrRjPGaxeAm9QpiFlFEkb3eyjNCJ3ur/view?usp=drive_link)  
[GDB Hardware Debugging_Main_Configuration](https://drive.google.com/file/d/1KYCKNr1jPEVpw2qq8PpECrbc_k2X_3TE/view?usp=sharing)  
[GDB Hardware Debugging_Debugger_Configuration](https://drive.google.com/file/d/1bes3LA_oOc4WGDeuwI9wwz-3gZbU3R1X/view?usp=drive_link)  
[GDB Hardware Debugging_Startup_Configuration](https://drive.google.com/file/d/1wdwB2Liqvi05xLNEKN0Tlf1TVIoUkDpR/view?usp=drive_link)

Follow these steps to set up GDB hardware debugging in STM32CubeIDE:

1. **Open your project**  
   Create a new STM32CubeIDE project or import an existing one.
2. **Add a Debug Configuration**  
   Go to **Run → Debug Configurations**, then double-click **GDB Hardware Debugging** to create a new entry.
3. **Configure the Debugger**

   - **GDB Command:** `arm-none-eabi-gdb`
   - **Debug Server:** Black Magic Probe
   - **Protocol:** `extended-remote`
   - **Connection:** `192.168.4.1:2345`

4. **Set Startup Commands**  
   Under the **Startup** tab, paste:

```
set logging on
set mem inaccessible-by-default off
monitor swdp_scan
attach 1

```

- For more details, see [Black Magic Probe STM32CubeIDE](https://black-magic.org/usage/stm32cubeide.html).

5. **Launch the Debugger**  
   Click **Apply**, then **Debug**. STM32CubeIDE will connect over Wi-Fi to your Black Magic Probe.
