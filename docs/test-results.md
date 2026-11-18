# Test Results

Results from the final project report (CPEN6254010 IoT Design & Application).

## 1. Heart rate vs. a commercial smartwatch
The oximeter was compared with a smartwatch worn at the same time, 7 readings.

| # | Smartwatch (BPM) | HaloHealth oximeter (BPM) |
|---|------------------|---------------------------|
| 1 | 95 | 99 |
| 2 | 96 | 99 |
| 3 | 95 | 99 |
| 4 | 94 | 99 |
| 5 | 95 | 99 |
| 6 | 93 | 99 |
| 7 | 92 | 99 |
| **Mean** | **94.3** | **99.0** |

- The smartwatch fluctuated between 92 and 96 BPM, while the oximeter stayed at a constant **99**, about 4.7 BPM (5%) above the smartwatch mean.
- A reading that never changes is a sign the algorithm was not seeing a pulse. The report attributes it to two causes:
  1. **Optical bypass:** the gap between the finger and the photodetector in the current casing lets ambient light or direct LED reflection reach the sensor, so the signal is not modulated by arterial blood volume.
  2. **AC/DC isolation:** the firmware did not isolate the small AC (pulsatile) component from the dominant DC baseline well enough, so an essentially flat signal could still pass validation.
- Conclusion in the report: the validation logic itself works, but it validated a compromised signal. Sensor placement and light shielding must be treated as design requirements equal to the software checks.

## 2. Pipeline and behavior
| Goal | Outcome |
|------|---------|
| Power efficiency (80 MHz, Bluetooth off, 10 s send interval) | Achieved |
| Validated data pipeline (finger threshold, correlation, ranges, smoothing) | Achieved |
| Wireless link C3 → S3 with ESP-NOW, no WiFi join on the sensor side | Achieved |
| Responsive UI during uploads (dual-core split) | Achieved |
| Real-time sync and persistent history in Firestore | Achieved |
| Measurement accuracy under real conditions | **Not met** (see above) |

Observations:
- The I2C watchdog recovered the sensor a few times during testing, likely due to a loose connector.
- A loosely placed finger sometimes reset the buffer mid-measurement, so measurement time varied between attempts.
- The Serial Monitor was used to verify each stage in isolation, and the Firebase Console to confirm that documents appeared under `users/{uid}/alat/Oximeter/riwayat` with correct fields. This caught a timestamp saved in the wrong format and a reading saved under the wrong user because of a Kit ID mismatch.
- The BPM once oscillated between two close values. The report traced it to a smoothing factor that reacted too strongly to small correlation changes. Separately, a comment in `algorithm_by_RF.h` explains that `min_autocorrelation_ratio` was raised from 0.35 to 0.5 to stop the algorithm locking onto a wrong neighboring period (for example flipping between 93 and 107 BPM).

## 3. Limitations of this test
- Only one comparison of 7 readings, against a consumer smartwatch rather than a medical reference, and with the finger-contact problem present.
- SpO2 was not compared against a reference device.
- The accuracy test should be repeated after the probe casing is fixed.
