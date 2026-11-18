# Third-Party Notices

| Component | Where | Notes |
|-----------|-------|-------|
| MAX30102 driver (`max30102.cpp/.h`) | `oximeter-c3/src/` | Based on Maxim Integrated's MAXREFDES117# driver, modified by Robert Fraczkiewicz for the Wire library. The original MIT-style license header is kept at the top of both files and must stay with them. |
| Heart rate / SpO2 algorithm (`algorithm_by_RF.cpp/.h`) | `oximeter-c3/src/` | From Robert Fraczkiewicz's [MAX30102_by_RF](https://github.com/aromring/MAX30102_by_RF) (see his [Instructable](https://www.instructables.com/id/Pulse-Oximeter-With-Much-Improved-Precision/)). Modified here: parabolic interpolation for a more precise BPM and a higher `min_autocorrelation_ratio` (0.5). Check the upstream repository for its license terms before redistributing. |
| TFT_eSPI | `kitbox-s3` (library dependency) | Installed by PlatformIO; see its own license. |
| Firebase Arduino Client Library for ESP8266 and ESP32 (Mobizt) | `kitbox-s3` (library dependency) | Installed by PlatformIO; see its own license. |
| Adafruit NeoPixel | `kitbox-s3` (library dependency) | Listed in `platformio.ini`, not used by the current code. |

The project's own code is under the [MIT License](LICENSE).
