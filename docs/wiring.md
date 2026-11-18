# Wiring

Schematics: [`oximeter`](../hardware/schematic/oximeter-esp32c3.png), [`kit box`](../hardware/schematic/kitbox-esp32s3.png).

## Oximeter unit
| MAX30102 pin | ESP32-C3 Super Mini |
|--------------|--------------------|
| SDA | GPIO8 |
| SCL | GPIO9 |
| VIN | 3V3 net |
| GND | GND |
| INT, IRD, RD | not connected (the sensor is polled over I2C) |

- Power comes from a JST-PH 2-pin battery connector feeding the board's supply pins. No extra regulation circuit is added.
- A 1 µF capacitor sits between the sensor supply (VIN) and GND.

## Kit box
| Function | ESP32-S3 pin |
|----------|--------------|
| TFT / touch MISO (SDO) | GPIO13 |
| TFT / touch MOSI (SDI) | GPIO11 |
| TFT / touch SCK | GPIO12 |
| TFT CS | GPIO10 |
| TFT DC | GPIO7 |
| TFT RST | GPIO6 |
| Touch CS (T_CS) | GPIO16 |
| Battery voltage (ADC) | GPIO5 |
| Hold-to-reset-ID button | GPIO0 (BOOT) |

These match the build flags in `kitbox-s3/platformio.ini`.

## Power (kit box)
- The battery feeds the MT3608 boost converter, which supplies the ESP32-S3 (5V pin) and the TFT.
- A TP4056 charging module connects to the battery through connector J1, so it can be charged in place.
- Two 1 kΩ resistors form a ÷2 divider from the battery line to GPIO5. The firmware computes `V = ADC * 3.3 / 4095 * 2 + 0.20` and maps 2.5-4.4 V to 0-100%.

## Notes
- The TFT touch controller uses the same SPI bus as the display, with its own chip select.
- If the display colors look swapped (for example a red button appearing blue), the panel's RGB/BGR order may need to be set in the TFT_eSPI build flags.
