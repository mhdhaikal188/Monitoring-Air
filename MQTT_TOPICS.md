# MQTT Topics Dashboard Monitoring Air

Root topic:

```text
tahaikal/device01
```

## 1. Telemetry ESP32 ke dashboard

Topic:

```text
tahaikal/device01/telemetry
```

Contoh payload:

```json
{
  "ph": 7.10,
  "tds": 285,
  "heightCm": 8.7,
  "targetHeightCm": 9,
  "pump1": true,
  "pump2": false,
  "mode": "AUTO"
}
```

Dashboard memakai `heightCm` langsung sebagai nilai ketinggian air. Tidak ada parameter jarak penuh, jarak kosong, batas bawah, atau batas atas pada dashboard.

## 2. Status perangkat

Topic:

```text
tahaikal/device01/status
```

Payload:

```text
online
```

atau:

```text
offline
```

## 3. Mode AUTO/MANUAL

Topic:

```text
tahaikal/device01/control/mode/set
```

Payload:

```text
AUTO
```

atau:

```text
MANUAL
```

## 4. Pompa 1 manual

Topic:

```text
tahaikal/device01/control/pump1/set
```

Payload:

```text
ON
```

atau:

```text
OFF
```

## 5. Pompa 2 manual

Topic:

```text
tahaikal/device01/control/pump2/set
```

Payload:

```text
ON
```

atau:

```text
OFF
```

## 6. Target ketinggian air

Topic:

```text
tahaikal/device01/control/level/set
```

Contoh target 9 cm:

```json
{
  "targetHeightCm": 9
}
```

Pada mode AUTO, contoh logika Pompa 1 pada program ESP32:

```text
heightCm < targetHeightCm   -> Pompa 1 ON
heightCm >= targetHeightCm  -> Pompa 1 OFF
```

Pompa 2 dapat dihubungkan ke hasil fuzzy atau proses filtrasi pada program utama.
