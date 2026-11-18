# Firestore Schema

```
users/{uid}
{
  "nama": string,
  "email": string,
  "jenisKelamin": string,
  "telepon": string,
  "usia": number,
  "kodeKit": string,          // 4-digit Kit Code printed on the kit box
  "createdAt": timestamp,
  "lastRiwayatID": string     // patched after every new reading
}

users/{uid}/alat/{namaAlat}   // namaAlat is always "Oximeter" for now
{
  "baterai": number           // device battery percentage (see note)
}

users/{uid}/alat/{namaAlat}/riwayat/{autoId}
{
  "bpm": integer,
  "saturasi": integer,
  "statusSaturasi": string,   // "Normal" | "Rendah" | "Kritis"
  "waktu": timestamp          // from the NTP-synced clock, stored as UTC
}
```

- History is a **sub-collection**, so it keeps growing instead of overwriting the last value. This is why Firestore was chosen over the Realtime Database.
- `kodeKit` is how the kit box finds its owner: it runs a Firestore query on `users` where `kodeKit == <Kit ID>`, and uses the returned document ID as `uid`.
- After each successful write, the firmware patches `users/{uid}.lastRiwayatID` with the new document ID.
- Note: the `baterai` field is part of the planned schema, but the current kit box firmware does not write it.

## Setting up Firebase
1. Create a Firebase project and enable **Cloud Firestore**.
2. Enable **Authentication → Email/Password** and create a dedicated account for the kit box.
3. Put the project ID, Web API key, and that account's credentials at the top of `kitbox-s3/src/main.cpp`.
4. Publish security rules before using real data: start from [`firebase/firestore.rules`](../firebase/firestore.rules) and read [`security.md`](security.md).
