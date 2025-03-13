# Overview

## Network and gdb

- BMP, `gdb_main` loop as a thread
- `network_gdb_server_task` as a thread
  - init network socket
  - listen for incoming connection (normally from gdb client)
  - accept incoming connection
  - receive data from incoming connection
  - send received data to `gdb-glue-receive`
- `gdb-glue-receive` send data to FreeRTOS stream buffer
- how received data from stream buffer is passed processed by `gdb_main` loop?
  - via interfaces functions
    - `unsigned char gdb_if_getchar_to(int timeout)`, client to gdb-server, rx
    - `unsigned char gdb_if_getchar(void) `, client to gdb-server, rx
    - `void gdb_if_putchar(unsigned char c, int flush)`, target to gdb-server, tx

### Progress

- [x] Upgrade network.c esp-idf_v4 to esp-idf_v5.0.8
- [ ] Porting network-gdb.c from esp32s2 to esp32

## Cli-UART

- Comment line interface to configure the esp32 via uart. Configurations are like wifi mode, wifi ssid, wifi password, etc.
- `cli-uart.c` Enable connection between desktop and esp32 via usb-uart bridge.Enter cli-uart mode by `screen /dev/ttyUSB0 -b 115200`. Commands like `device-info`, `config_set_wifi_mode AP` to configure the esp32
  - [ ] Porting /cli from esp32s2 to esp32, not using simple-uart
  - [ ] Porting cli-uart.c from esp32s2 to esp32, not using simple-uart
