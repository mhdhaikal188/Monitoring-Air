# Dashboard Monitoring Air — HTML Satu File

Dashboard ini hanya memakai satu file utama: `index.html`.

## Fitur

- Data pH
- Data TDS dalam ppm
- Jarak sensor ultrasonik
- Persentase ketinggian air
- Keputusan `BAIK` atau `TIDAK BAIK`
- Grafik pH, TDS, jarak, dan ketinggian
- Riwayat pengukuran di browser
- Ekspor CSV
- Mode AUTO dan MANUAL
- Kontrol Pompa 1 dan Pompa 2
- Pengaturan jarak tangki penuh/kosong
- Pengaturan batas rendah/tinggi
- Mode demo
- Koneksi HiveMQ Cloud menggunakan Secure WebSocket

## Konfigurasi MQTT

- Permission: `tahaikal`
- Topic filter: `tahaikal/device01/#`
- Credential dashboard: `dashboardtahaikal`
- Root topic: `tahaikal`
- Device ID: `device01`
- Port dashboard: `8884`
- WebSocket path: `/mqtt`

## Topic

Data ESP32:

```text
tahaikal/device01/telemetry
```

Payload:

```json
{
  "ph": 7.12,
  "tds": 286,
  "distance": 14.8,
  "pump1": false,
  "pump2": false,
  "mode": "AUTO"
}
```

Status ESP32:

```text
tahaikal/device01/status
```

Payload: `online` atau `offline`.

Kontrol dashboard:

```text
tahaikal/device01/control/mode/set
tahaikal/device01/control/pump1/set
tahaikal/device01/control/pump2/set
tahaikal/device01/control/thresholds/set
```

## Upload ke GitHub

1. Buat repository baru.
2. Klik **uploading an existing file**.
3. Upload `index.html` ke root repository.
4. Commit perubahan.

## Deploy ke Vercel

1. Masuk ke Vercel menggunakan GitHub.
2. Klik **Add New → Project**.
3. Import repository.
4. Framework Preset: **Other**.
5. Root Directory: `./`.
6. Build Command: kosong.
7. Output Directory: kosong.
8. Klik **Deploy**.

Karena ini situs statis, tidak diperlukan `npm install`, Vite, React, Node.js, atau Docker.

## Catatan keamanan

Password MQTT dimasukkan melalui halaman dashboard dan tidak disimpan permanen oleh dashboard. Walau demikian, aplikasi browser tidak dapat menyembunyikan credential dari pengguna yang membuka Developer Tools. Gunakan permission HiveMQ yang dibatasi hanya untuk `tahaikal/device01/#`.


## Program uji ESP32

File berikut disertakan:

```text
esp32/esp32_mqtt_html_dashboard.ino
```

Program mengirim data contoh setiap 5 detik. Isi Wi-Fi, hostname HiveMQ,
username `esp32tahaikal`, dan password credential ESP32 sebelum upload.
