# Topik MQTT — Satu Credential

Credential yang digunakan:

```text
Username   : tahaikal
Permission : PUBLISH_SUBSCRIBE
```

Credential yang sama dipakai oleh ESP32 dan dashboard.

## ESP32 ke dashboard

Telemetry:

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

Status:

```text
tahaikal/device01/status
```

Payload:

```text
online
offline
```

## Dashboard ke ESP32

Mode:

```text
tahaikal/device01/control/mode/set
```

Payload:

```text
AUTO
MANUAL
```

Pompa 1:

```text
tahaikal/device01/control/pump1/set
```

Payload:

```text
ON
OFF
```

Pengaturan ketinggian:

```text
tahaikal/device01/control/level/set
```

Payload:

```json
{
  "fullDistance": 5,
  "emptyDistance": 35,
  "lowLevel": 5,
  "highLevel": 25
}
```
