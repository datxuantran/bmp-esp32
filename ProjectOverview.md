# Overview

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

# Progress

- `cli-uart.c` Enable connection between desktop and esp32 via usb-uart bridge
  - Enter cli-uart mode by `screen /dev/ttyUSB0 -b 115200`
  - Commands like `device-info`, `config_set_wifi_mode AP` to configure the esp32
- [ ] Porting /cli from esp32s2 to esp32
  - [ ] not using simple-uart.c
- [ ] Porting cli-uart.c from esp32s2 to esp32
  - [ ] not using simple-uart.c
- [ ] Porting network-gdb.c from esp32s2 to esp32
- [ ] Porting network.c from esp32s2 to esp32
  - [ ] Differences in esp32 and esp32s2 network stack
- [ ] Porting nvs-config.c from esp32 to esp32
  - [ ] Differences in esp32 and esp32s2 nvs stack
- [ ] Porting nvs.c from esp32 to esp32
