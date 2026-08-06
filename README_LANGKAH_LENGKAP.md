# Langkah Lengkap — Satu Credential HiveMQ

Paket ini menggunakan satu credential saja untuk ESP32 dan dashboard:

```text
Username   : tahaikal
Permission : PUBLISH_SUBSCRIBE
```

Password credential yang sama dimasukkan ke program ESP32 dan `index.html`.

> Catatan keamanan: penggunaan satu credential lebih sederhana, tetapi password
> dashboard dapat dilihat melalui source code browser. Jangan gunakan credential
> administrator dan batasi permission hanya untuk topik proyek.

---

## 1. Periksa credential HiveMQ

Pada HiveMQ Cloud buka bagian:

```text
Access Management
→ Credentials
```

Pastikan terdapat credential:

```text
Name       : tahaikal
Permission : PUBLISH_SUBSCRIBE
```

Gunakan password credential `tahaikal` yang telah dibuat.

Jika lupa password, buat ulang atau reset password credential tersebut.

---

## 2. Periksa permission

Buka:

```text
Access Management
→ Permissions
```

Permission `PUBLISH_SUBSCRIBE` harus mengizinkan:

```text
Publish   : Allow
Subscribe : Allow
QoS 0     : Allow
QoS 1     : Allow
Retained  : Allow
```

Gunakan topic filter:

```text
tahaikal/device01/#
```

Jika permission bawaan tidak menampilkan topic filter, pastikan permission tersebut
memang mengizinkan publish dan subscribe untuk topik proyek.

---

## 3. Ekstrak ZIP

Ekstrak:

```text
dashboard-monitoring-air-satu-credential.zip
```

Struktur:

```text
dashboard-monitoring-air-satu-credential/
├── index.html
├── README_LANGKAP.md
├── MQTT_TOPICS.md
└── esp32/
    └── esp32_monitoring_air_satu_credential.ino
```

---

## 4. Edit program ESP32

Buka:

```text
esp32/esp32_monitoring_air_satu_credential.ino
```

Isi Wi-Fi:

```cpp
const char* WIFI_SSID = "NAMA_WIFI";
const char* WIFI_PASSWORD = "PASSWORD_WIFI";
```

Isi credential HiveMQ:

```cpp
const char* MQTT_USERNAME = "tahaikal";
const char* MQTT_PASSWORD = "PASSWORD_CREDENTIAL_TAHAIKAL";
```

Host dan port:

```cpp
const char* MQTT_HOST =
  "6e97312e16344652aaddfd835947cb0f.s1.eu.hivemq.cloud";

const uint16_t MQTT_PORT = 8883;
```

ESP32 menggunakan port `8883`.

---

## 5. Instal library Arduino

Instal:

```text
PubSubClient by Nick O'Leary
ArduinoJson by Benoit Blanchon
```

Pilih board:

```text
ESP32 Dev Module
```

---

## 6. Upload program ESP32

1. Hubungkan ESP32.
2. Pilih port COM.
3. Klik Verify.
4. Klik Upload.
5. Buka Serial Monitor pada 115200.

Hasil yang benar:

```text
Wi-Fi terhubung.
Menghubungkan HiveMQ... berhasil.
Telemetry: {...}
Publish berhasil.
```

Jika muncul:

```text
Kode MQTT = 4
```

username atau password salah.

Jika muncul:

```text
Kode MQTT = 5
```

credential atau permission ditolak.

---

## 7. Edit dashboard

Buka:

```text
index.html
```

Cari:

```javascript
const MQTT_CONFIG = Object.freeze({
  host: "6e97312e16344652aaddfd835947cb0f.s1.eu.hivemq.cloud",
  port: 8884,
  path: "/mqtt",
  username: "tahaikal",
  password: "GANTI_PASSWORD_CREDENTIAL_TAHAIKAL",
  rootTopic: "tahaikal",
  deviceId: "device01"
});
```

Ganti:

```javascript
password: "GANTI_PASSWORD_CREDENTIAL_TAHAIKAL"
```

dengan password credential yang sama seperti pada ESP32.

Dashboard menggunakan:

```text
Username : tahaikal
Port     : 8884
Path     : /mqtt
```

---

## 8. Uji dashboard lokal

Klik dua kali `index.html`.

Status seharusnya berubah:

```text
Tidak Terhubung
→ Terhubung
```

Jika berhasil, kartu status MQTT berubah hijau.

Jika belum terhubung, periksa:

```text
Host
Port 8884
Path /mqtt
Username tahaikal
Password credential
Permission PUBLISH_SUBSCRIBE
```

---

## 9. Periksa data telemetry

ESP32 mengirim ke:

```text
tahaikal/device01/telemetry
```

Payload:

```json
{
  "ph": 7.1,
  "tds": 285,
  "distance": 15,
  "heightCm": 20,
  "pump1": false,
  "mode": "AUTO"
}
```

Dashboard akan menampilkan:

```text
pH         : 7,10
TDS        : 285 ppm
Jarak      : 15 cm
Ketinggian : 20 cm
```

---

## 10. Pengaturan ketinggian dalam cm

Contoh:

```text
Jarak penuh  : 5 cm
Jarak kosong : 35 cm
Batas bawah  : 5 cm
Batas atas   : 25 cm
```

Logika AUTO:

```text
Ketinggian <= 5 cm  → Pompa ON
Ketinggian >= 25 cm → Pompa OFF
```

Klik:

```text
Simpan Lokal
Kirim ke ESP32
```

---

## 11. Uji mode manual

1. Klik MANUAL.
2. Klik Pompa ON.
3. Periksa relay.
4. Klik Pompa OFF.
5. Setelah pengujian selesai, kembali ke AUTO.

---

## 12. Upload GitHub

Upload ke root repository:

```text
index.html
README_LANGKAP.md
MQTT_TOPICS.md
```

Pastikan `index.html` berada langsung di root repository.

> Jangan unggah program ESP32 ke repository publik apabila masih berisi password.

---

## 13. Deploy Vercel

Gunakan:

```text
Framework Preset : Other
Root Directory   : ./
Build Command    : kosong
Output Directory : kosong
Install Command  : kosong
```

Klik Deploy.

Setelah selesai, tekan:

```text
Ctrl + Shift + R
```

agar browser tidak memakai cache dashboard lama.

---

## 14. Urutan pemeriksaan jika gagal

```text
1. ESP32 berhasil terhubung Wi-Fi.
2. Serial Monitor menampilkan HiveMQ berhasil.
3. Username ESP32 adalah tahaikal.
4. Password ESP32 benar.
5. Username dashboard adalah tahaikal.
6. Password dashboard sama dengan ESP32.
7. ESP32 memakai port 8883.
8. Dashboard memakai port 8884 dan path /mqtt.
9. Permission PUBLISH_SUBSCRIBE mengizinkan publish dan subscribe.
10. Topic yang digunakan adalah tahaikal/device01/#.
```

---

## Catatan keamanan

Karena password yang sama dimasukkan ke dashboard, siapa pun yang membuka
Developer Tools browser dapat melihat password tersebut. Gunakan credential ini
hanya untuk proyek dan jangan gunakan password yang sama dengan akun penting lain.
