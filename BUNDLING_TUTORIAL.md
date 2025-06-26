# Tutorial: Bundling Firmware into a Single Image

This guide explains how to combine the bootloader, partition table, and application firmware into a single binary file. This is useful for production programming or for quickly flashing the entire firmware onto an ESP32 without needing to flash each part separately.

## Prerequisites

- You have successfully set up the ESP-IDF environment as described in the main [TUTORIAL.md](TUTORIAL.md).
- You have built the project at least once using `idf.py build`.

## Step 1: Identify Build Artifacts and Flash Offsets

After a successful build with `idf.py build`, the ESP-IDF build system generates the necessary binary files and prints a summary of which files to flash at which offsets.

1. Run the build command if you haven't already:

   ```bash
   idf.py build
   ```

2. At the end of the build output, look for a table that looks like this:

   ```
   esptool.py v4.5.1
   Creating esp32 image...
   Merged 2 ELF sections
   Successfully created esp32 image.
   Generated /home/dat/esp/bmp-esp32/build/bootloader/bootloader.bin
   Generated /home/dat/esp/bmp-esp32/build/partition_table/partition-table.bin
   Generated /home/dat/esp/bmp-esp32/build/ota_data_initial.bin
   Generated /home/dat/esp/bmp-esp32/build/bmp-esp32.bin
   Flash type: dio
   Flash frequency: 40m
   Flash size: 4MB
   Project build complete. To flash, run this command:
   /home/dat/esp/esp-idf-v5.0.8/components/esptool_py/esptool/esptool.py -p (PORT) -b 460800 --before default_reset --after hard_reset --chip esp32  write_flash --flash_mode dio --flash_freq 40m --flash_size 4MB 0x1000 build/bootloader/bootloader.bin 0x8000 build/partition_table/partition-table.bin 0x10000 build/bmp-esp32.bin
   ```

3. From the output, note the following:

   - **Bootloader offset and file:** `0x1000 build/bootloader/bootloader.bin`
   - **Partition table offset and file:** `0x8000 build/partition_table/partition-table.bin`
   - **Application offset and file:** `0x10000 build/bmp-esp32.bin`

   These offsets and file paths are crucial for the next step.

## Step 2: Merge the Binaries into a Single File

Now, you will use the `esptool.py` utility to combine these files into a single firmware image.

1. Run the following command, replacing the offsets with the ones you identified in the previous step.

   ```bash
   esptool.py --chip esp32 merge_bin -o firmware.bin --flash_mode dio --flash_freq 40m --flash_size 4MB \
   0x1000 build/bootloader/bootloader.bin \
   0x8000 build/partition_table/partition-table.bin \
   0x10000 build/bmp-esp32.bin
   ```

2. This command will create a new file named `firmware.bin` in your project's root directory. This file contains the entire firmware.

## Step 3: Flash the Bundled Image

With the single bundled image, you can now flash the ESP32 with a single command.

1. Connect your ESP32 board to your computer.

2. Run the following `esptool.py` command to flash the image. Replace `/dev/ttyUSB0` with the correct serial port for your device.

   ```bash
   esptool.py --chip esp32 write_flash --flash_mode dio --flash_freq 40m --flash_size 4MB 0x0 firmware.bin
   ```

   **Note:** The bundled image is always flashed at the starting offset `0x0`.

Your ESP32 is now fully programmed with the Black Magic Probe firmware.
