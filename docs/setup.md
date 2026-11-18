# Setup Guide

## Prerequisites
- [PlatformIO](https://platformio.org/) (VS Code extension or CLI)
- ESP32-C3 Super Mini + MAX30102 (oximeter), ESP32-S3 + 2.8" ILI9341 touch TFT (kit box)
- A WiFi network with internet access, and a Firebase project

## Values you must set
| Placeholder in the code | What to put |
|-------------------------|-------------|
| `NAMA WIFI` | Name of your WiFi network (both boards must use the same network) |
| `PASS WIFI` | WiFi password |
| `EMAIL FIREBASE` | Email of the dedicated kit box account in Firebase Authentication |
| `PASS FIREBASE` | Password of that account |
| `API KEY FIREBASE`, `ID PROJECT FIREBASE`, `ID-PROJECT-FIREBASE` | Web API key and project ID from the Firebase console |
| `s3Address` (oximeter) | MAC address of the kit box, shown in its TFT footer |

Do not commit real values: put the placeholders back (or review `git diff`) before every commit.

## 1. Kit box (ESP32-S3)
```bash
cd kitbox-s3
# edit the placeholders at the top of src/main.cpp first
pio run -t upload
pio device monitor
```
- The project uses the pioarduino ESP32 platform (see `platformio.ini`) and these libraries, which PlatformIO installs automatically: `TFT_eSPI`, `Firebase Arduino Client Library for ESP8266 and ESP32`, `Adafruit NeoPixel` (listed as a dependency but not used by the current code).
- TFT pins and the ILI9341 driver are set through build flags, no `User_Setup.h` edit needed.
- On boot the kit box connects to WiFi, starts ESP-NOW, syncs NTP, signs in to Firebase, and looks up the owner of its Kit ID. Progress is shown on the TFT.
- Note the **MAC address** and **WiFi channel** printed in the TFT footer (also in the Serial log).

Serial commands: `STATUS` prints Kit ID, user ID, time, NTP state, and memory; `NEWID` generates a new Kit ID.

## 2. Oximeter (ESP32-C3)
```bash
cd oximeter-c3
# set s3Address and WIFI_SSID in src/main.cpp first
pio run -t upload
pio device monitor
```
- The oximeter does not join WiFi. It only scans for `WIFI_SSID` to copy its channel, so use the **same network** the kit box uses.
- Place a finger on the sensor. The Serial log shows `Mengumpulkan data...` until the 100-sample buffer fills, then BPM/SpO2.

## 3. Pair with a user
1. Read the **Kode Kit** on the kit box main screen.
2. Enter it in the mobile app when registering. The app stores it as `kodeKit` on the user document.
3. After a measurement, tap **YA** on the kit box to save it. The reading appears in the app's history.

## Troubleshooting
| Symptom | Likely cause |
|---------|--------------|
| `ESP-NOW: Gagal kirim` on the oximeter | Wrong MAC in `main.cpp` (`s3Address`), or the two boards are on different WiFi channels |
| `Mencari user...` never finishes | No user document with a matching `kodeKit`, or the kit box account lacks permission |
| `[MAX30102] ERROR: Sensor tidak ditemukan` | Check SDA/SCL (GPIO8/GPIO9) and power; some cheap modules also swap the red/IR channels |
| `Sinyal tidak stabil...` | Poor finger contact or motion; correlation below 0.78 |
| Timestamps wrong or empty | NTP sync failed at boot; check internet access and reboot |
