# Changelog

## [Unreleased]
- Probe casing rework and accuracy re-test
- Interrupt-driven FIFO reading, offline queue, per-device authentication

## [0.1.0-prototype]
- Oximeter unit: MAX30102 + ESP32-C3, validation and smoothing, ESP-NOW sender
- Kit box: ESP32-S3 with ILI9341 touchscreen, dual-core RTOS, confirmation before saving
- Firestore history with NTP timestamps and a Kit Code pairing flow
- Credentials replaced by visible placeholders (`NAMA WIFI`, `PASS WIFI`, `EMAIL FIREBASE`, `PASS FIREBASE`, ...)
- Kit lookup query limited to one document
- Recommended Firestore rules added in `firebase/firestore.rules`
