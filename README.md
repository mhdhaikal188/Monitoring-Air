# Dashboard HTML MQTT Tahaikal — Koneksi Otomatis

Dashboard akan langsung mencoba terhubung ke HiveMQ Cloud ketika halaman dibuka.

## File utama

```text
index.html
```

## Konfigurasi yang harus diedit

Buka `index.html`, cari:

```javascript
const AUTO_MQTT_CONFIG = Object.freeze({
  host: "GANTI_DENGAN_HOST_HIVEMQ",
  port: 8884,
  username: "dashboardtahaikal",
  password: "GANTI_DENGAN_PASSWORD_DASHBOARD",
  rootTopic: "tahaikal",
  deviceId: "device01",
  autoConnect: true
});
```

Ganti `host` dan `password`.

Contoh:

```javascript
const AUTO_MQTT_CONFIG = Object.freeze({
  host: "xxxxxxxxxxxxxxxx.s1.eu.hivemq.cloud",
  port: 8884,
  username: "dashboardtahaikal",
  password: "PasswordDashboardAnda",
  rootTopic: "tahaikal",
  deviceId: "device01",
  autoConnect: true
});
```

Host ditulis tanpa `https://`, `wss://`, atau `/mqtt`.

## HiveMQ

```text
Permission name : tahaikal
Topic filter    : tahaikal/device01/#
Dashboard user  : dashboardtahaikal
Port dashboard  : 8884
```

Dashboard otomatis subscribe ke:

```text
tahaikal/device01/telemetry
tahaikal/device01/status
```

## GitHub

Upload file berikut ke root repository:

```text
index.html
README.md
MQTT_TOPICS.md
```

Jangan mengunggah ZIP saja.

## Vercel

```text
Framework Preset : Other
Root Directory   : ./
Build Command    : kosong
Output Directory : .
Install Command  : kosong
```

Setelah deployment selesai, buka URL Vercel. Dashboard akan terhubung otomatis.

## Catatan keamanan penting

Dashboard berbasis HTML berjalan di browser. Credential yang ditulis di `index.html`
dapat dilihat melalui source code atau Developer Tools. Oleh karena itu:

1. Pakai credential khusus dashboard, bukan credential ESP32.
2. Batasi permission hanya ke `tahaikal/device01/#`.
3. Jangan gunakan credential administrator.
4. Jika repository GitHub bersifat publik, password juga akan terlihat di GitHub.
   Gunakan repository private untuk mengurangi paparan, tetapi pengguna halaman tetap
   dapat melihat credential melalui browser.
