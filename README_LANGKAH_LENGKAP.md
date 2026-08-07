# Dashboard Monitoring Air — Pompa 1, Pompa 2, dan Satu Target Ketinggian

Versi ini dibuat dengan kontrol ketinggian yang sederhana. Dashboard hanya memiliki satu pengaturan, yaitu **Target Ketinggian Air (cm)**. Contoh target yang digunakan adalah **9 cm**.

## Isi paket

- `index.html` — dashboard web.
- `MQTT_TOPICS.md` — daftar topic dan payload MQTT.
- `esp32/esp32_monitoring_air/esp32_monitoring_air.ino` — contoh program ESP32 untuk komunikasi dashboard.

## Perubahan utama

Dashboard sekarang memiliki:

- Nilai pH.
- Nilai TDS.
- Ketinggian air dalam cm.
- Keputusan kualitas air.
- Status Pompa 1.
- Status Pompa 2.
- Mode AUTO dan MANUAL.
- Satu input **Target Ketinggian Air**.

Tidak ada lagi pengaturan batas bawah dan batas atas. Dashboard juga tidak memakai parameter jarak penuh dan jarak kosong. Nilai `heightCm` diterima langsung dari ESP32.

## Logika target 9 cm

Jika target di dashboard diisi:

```text
9
```

maka ESP32 menerima:

```json
{
  "targetHeightCm": 9
}
```

Pada contoh program:

```text
Ketinggian air < 9 cm   -> Pompa 1 ON
Ketinggian air >= 9 cm  -> Pompa 1 OFF
```

Pompa 2 tidak mengikuti target ketinggian. Pompa 2 dapat tetap digunakan untuk hasil fuzzy, filtrasi, atau dapat dikontrol manual saat mode MANUAL.

---

# A. Menyiapkan Arduino IDE

## 1. Board ESP32

Di Arduino IDE buka:

```text
Tools > Board > ESP32 Arduino > ESP32 Dev Module
```

Jika pilihan ESP32 belum ada, instal paket board ESP32 melalui Boards Manager.

## 2. Library

Instal library berikut melalui Library Manager:

```text
PubSubClient
ArduinoJson
```

Library `WiFi` dan `WiFiClientSecure` sudah tersedia pada paket board ESP32.

## 3. Buka program

Buka:

```text
esp32/esp32_monitoring_air/esp32_monitoring_air.ino
```

---

# B. Mengatur Wi-Fi dan HiveMQ

Pada program ESP32, sesuaikan:

```cpp
const char* WIFI_SSID = "NAMA_WIFI";
const char* WIFI_PASSWORD = "PASSWORD_WIFI";
```

Kemudian periksa konfigurasi HiveMQ:

```cpp
const char* MQTT_HOST = "HOST_HIVEMQ";
const uint16_t MQTT_PORT = 8883;
const char* MQTT_USERNAME = "USERNAME";
const char* MQTT_PASSWORD = "PASSWORD";
```

Konfigurasi host, username, password, root topic, dan device ID pada `index.html` harus sama dengan ESP32.

Dashboard browser menggunakan koneksi WebSocket Secure pada port **8884**, sedangkan ESP32 menggunakan MQTT TLS pada port **8883**.

---

# C. Koneksi contoh pin

Program contoh menggunakan:

```text
Relay Pompa 1 : GPIO 26
Relay Pompa 2 : GPIO 27
TRIG ultrasonik: GPIO 5
ECHO ultrasonik: GPIO 18
```

Sesuaikan pin dengan rangkaian alat yang sebenarnya.

Jika pin ECHO sensor ultrasonik menghasilkan sinyal 5 V, gunakan pembagi tegangan atau level shifter agar tegangan ke GPIO ESP32 aman pada level sekitar 3,3 V.

---

# D. Pembacaan ketinggian air

Program contoh memiliki fungsi:

```cpp
float readWaterHeightCm()
```

Fungsi tersebut menghasilkan satu nilai cm dan nilai itu dikirim sebagai:

```json
"heightCm": 8.7
```

Jika program utama Anda sudah mempunyai variabel ketinggian air, paling aman adalah mengganti isi `readWaterHeightCm()` dengan nilai dari program utama tersebut.

Contoh:

```cpp
float readWaterHeightCm() {
  return ketinggianAir;
}
```

Dengan cara ini dashboard tidak melakukan perhitungan tambahan. Dashboard hanya menerima dan menampilkan nilai `heightCm`.

**Catatan penting:** pada contoh `.ino`, pembacaan jarak ultrasonik digunakan langsung sebagai nilai cm. Jika sensor Anda dipasang di atas penampung dan yang dibutuhkan adalah tinggi air dari dasar, nilai tersebut harus sudah dihitung pada program utama sesuai pemasangan sensor Anda sebelum dikirim sebagai `heightCm`.

---

# E. Upload program ESP32

1. Hubungkan ESP32 ke komputer.
2. Pilih board ESP32 Dev Module.
3. Pilih COM port ESP32.
4. Klik **Verify**.
5. Jika tidak ada error, klik **Upload**.
6. Buka Serial Monitor.
7. Gunakan baud rate:

```text
115200
```

Jika berhasil, Serial Monitor akan menampilkan koneksi Wi-Fi, koneksi HiveMQ, dan payload telemetry.

Contoh:

```json
{
  "ph": 7.1,
  "tds": 285,
  "heightCm": 8.7,
  "targetHeightCm": 9,
  "pump1": true,
  "pump2": false,
  "mode": "AUTO"
}
```

---

# F. Membuka dashboard

Buka file:

```text
index.html
```

Untuk pengujian lokal, file dapat dibuka langsung menggunakan browser. Jika browser membatasi fungsi tertentu, gunakan Live Server di Visual Studio Code.

Setelah dibuka, periksa bagian **Status MQTT**. Jika berhasil, status akan berubah menjadi:

```text
Terhubung
```

Dashboard akan menampilkan pH, TDS, ketinggian air, keputusan kualitas, Pompa 1, dan Pompa 2.

---

# G. Mengatur target 9 cm

Pada dashboard cari:

```text
Kontrol Ketinggian Air
```

Pilih mode:

```text
AUTO
```

Pada kolom:

```text
Target Ketinggian Air
```

isi:

```text
9
```

Kemudian klik:

```text
Kirim Target ke ESP32
```

Dashboard mengirim topic:

```text
tahaikal/device01/control/level/set
```

Payload:

```json
{
  "targetHeightCm": 9
}
```

ESP32 menyimpan target baru ke variabel:

```cpp
targetHeightCm
```

---

# H. Mode AUTO

Pada mode AUTO, tombol manual Pompa 1 dan Pompa 2 tidak menjadi pengendali utama.

Contoh target 9 cm:

```text
8.0 cm -> Pompa 1 ON
8.8 cm -> Pompa 1 ON
9.0 cm -> Pompa 1 OFF
9.5 cm -> Pompa 1 OFF
```

Logika ini terdapat pada:

```cpp
void updateAutomaticPump1(float heightCm)
```

---

# I. Mode MANUAL

Pilih:

```text
MANUAL
```

Setelah itu tombol berikut dapat digunakan:

```text
Pompa 1 ON
Pompa 1 OFF
Pompa 2 ON
Pompa 2 OFF
```

Topic Pompa 1:

```text
tahaikal/device01/control/pump1/set
```

Topic Pompa 2:

```text
tahaikal/device01/control/pump2/set
```

Payload masing-masing:

```text
ON
```

atau:

```text
OFF
```

---

# J. Menggabungkan dengan program pH, TDS, dan fuzzy utama

Pada file contoh, fungsi berikut masih contoh:

```cpp
float readPh()
float readTds()
float readWaterHeightCm()
```

Ganti nilai contoh dengan fungsi pembacaan dari program utama Anda.

Misalnya:

```cpp
float readPh() {
  return nilaiPH;
}

float readTds() {
  return nilaiTDS;
}

float readWaterHeightCm() {
  return ketinggianAir;
}
```

Untuk Pompa 2, hasil fuzzy dapat diarahkan ke:

```cpp
writePump2(true);
```

atau:

```cpp
writePump2(false);
```

Contoh sederhana:

```cpp
if (hasilFuzzyTidakBaik) {
  writePump2(true);
} else {
  writePump2(false);
}
```

---

# K. Deploy ke GitHub dan Vercel

## GitHub

1. Buat repository baru.
2. Upload `index.html` ke repository tersebut.
3. Commit perubahan.

## Vercel

1. Login ke Vercel.
2. Pilih **Add New Project**.
3. Hubungkan akun GitHub.
4. Pilih repository dashboard.
5. Framework preset dapat menggunakan **Other**.
6. Root directory biarkan pada folder yang berisi `index.html`.
7. Klik **Deploy**.

Setelah deploy selesai, buka URL Vercel dan periksa Status MQTT.

---

# L. Jika dashboard tidak terhubung

Periksa hal berikut:

1. Host HiveMQ dashboard sama dengan host ESP32.
2. Username dan password sama.
3. ESP32 menggunakan port 8883.
4. Dashboard menggunakan WebSocket Secure port 8884.
5. Permission credential HiveMQ mengizinkan publish dan subscribe pada root topic yang digunakan.
6. Topic ESP32 dan dashboard sama-sama menggunakan:

```text
tahaikal/device01
```

7. Internet komputer/HP dan ESP32 aktif.
8. Buka Developer Tools browser dan periksa Console jika ada error.

---

# M. Ringkasan topic

```text
tahaikal/device01/telemetry
tahaikal/device01/status
tahaikal/device01/control/mode/set
tahaikal/device01/control/pump1/set
tahaikal/device01/control/pump2/set
tahaikal/device01/control/level/set
```

Target 9 cm dikirim dengan:

```json
{"targetHeightCm":9}
```

Dashboard tidak memiliki pengaturan lain untuk ketinggian selain nilai target tersebut.


## Revisi Tampilan Status Pompa

Status **Pompa 1** dan **Pompa 2** sekarang ditempatkan pada deretan kartu utama di bagian atas dashboard, tepat setelah kartu **Keputusan**. Status tersebut tidak lagi ditampilkan pada panel Ketinggian Air sehingga informasi utama dapat dilihat langsung tanpa menggulir halaman.

Urutan kartu bagian atas sekarang:

1. Nilai pH
2. TDS
3. Ketinggian Air
4. Keputusan
5. Pompa 1
6. Pompa 2

Nilai status tetap diperbarui otomatis dari telemetry MQTT (`pump1` dan `pump2`).
