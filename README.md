# MatterP4C6Lab

Minimal ESP32-P4 plus ESP32-C6 Matter-over-WiFi lab firmware for Hubitat
commissioning tests.

The ESP32-P4 runs the Matter application. Wi-Fi is provided by the ESP32-C6
coprocessor through ESP-Hosted over SDIO. The firmware exposes one Matter
Dimmable Light endpoint and logs commissioning, On/Off, and Level Control
activity to serial.

The Matter Basic Information identity is intentionally unique to this lab
firmware:

```text
manufacturer: ACLYS_LAB
model: MatterP4C6Lab
vendor id: 0xFFF1
product id: 0x8002
```

## Wi-Fi

Create `main/wifi_secrets.h` from the example before building:

```c
#pragma once

#define LAB_WIFI_SSID "your-ssid"
#define LAB_WIFI_PASSWORD "your-password"
```

`main/wifi_secrets.h` is ignored by git.

## Build And Flash

This project vendors `components/esp_wifi_remote` from Espressif's 0.7.2
component with a small IDF 6.0 compatibility fallback. ESP32-P4 uses this
component to reach the ESP32-C6 Wi-Fi coprocessor over ESP-Hosted SDIO.

```sh
source /Users/csteele/.espressif/v6.0.2/esp-idf/export.sh
idf.py set-target esp32p4
idf.py build
idf.py -p /dev/cu.usbmodem5B910631151 flash monitor
```

## Pairing

The firmware logs the manual pairing code on boot. With the current defaults it
should be:

```text
34970112332
```

## Reset Matter State

Before pairing the board to a different hub, erase the NVS partition so the
stored Matter fabric state is clean:

```sh
python -m esptool --chip esp32p4 -p /dev/cu.usbmodem5B910631151 erase-region 0x11000 0x6000
```

Then reset or power-cycle the board and pair it again.
