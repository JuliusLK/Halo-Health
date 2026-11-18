# Security Notes

## Keep credentials out of git
- The committed code only contains placeholders (`NAMA WIFI`, `PASS WIFI`, `EMAIL FIREBASE`, `PASS FIREBASE`, and so on). After you type real values into `main.cpp` to test on a device, **never commit that file as is**: restore the placeholders, or run `git diff` before every commit and check that no real value appears. Keep your real values in a private note outside the repository.
- If a credential was ever committed or shared, **rotate it** (change the WiFi password, change the Firebase account password, and consider restricting the API key in the Google Cloud console). Removing the file later does not remove it from git history.

## Firestore rules
The kit box signs in with one email/password account and must be able to:
1. look up the owner of a Kit Code, and
2. create history documents and patch `lastRiwayatID` for that owner.

Things to watch when writing rules:
- **Do not let the kit account read whole user profiles.** The lookup query runs on `users`, whose documents contain name, email, phone, gender, and age. Anyone holding the kit account (its password is stored in the device flash) could read them if rules allow it. A safer design is a separate collection such as `kits/{kodeKit}` that only maps a Kit Code to a `uid`.
- Limit the kit account's writes to `users/{uid}/alat/Oximeter/riwayat/*` and the `lastRiwayatID` field.
- Let normal app users read and write only their own `users/{uid}` tree.
- Prefer a **per-device** identity over one shared account, so a single lost kit can be revoked.

## Other points
- ESP-NOW is used without encryption here (`peerInfo.encrypt = false`); a nearby device that knows the kit box MAC could inject readings, although the user still has to confirm them on the TFT. ESP-NOW supports encrypted peers if you need it.
- Kit pairing relies on a 4-digit code (1000-9999), which is easy to guess. For real use, add a stronger pairing step.

## Recommended Firestore rules
The file [`firebase/firestore.rules`](../firebase/firestore.rules) implements the points above for the current schema.

**Do not use this pattern for real data:**
```
match /{document=**} {
  allow read, write: if request.auth != null;
}
```
It lets **any signed-in account**, including every app user and the kit box account, read and write **every document** (all profiles and all health histories). It is fine for a quick demo, but not for real people's data.

What the recommended rules do:
- A signed-in app user can read and write only their own `users/{uid}` document and everything below it.
- The kit box account (matched by its email) can: look up an owner with a query limited to one document, create history documents with a valid shape (`bpm`, `saturasi`, `statusSaturasi`, `waktu`), and change only the `lastRiwayatID` field of the user document. It cannot read histories or other collections.
- Everything else is denied.

Before publishing:
1. Replace `EMAIL FIREBASE` in the rules with the kit box account's email, **in the Firebase console copy** (the committed file keeps the placeholder).
2. Copy your current rules somewhere as a backup.
3. Test with the **Rules Playground** and with the real app. The rules were written from the firmware and schema in this repository; if the app performs other cross-user reads or writes, those will be denied and the rules need adjusting.
4. The kit lookup returns a whole user document (name, email, phone) to the kit account. To avoid that entirely, move the Kit Code mapping to a separate `kits/{kodeKit}` collection that stores only the `uid`.
