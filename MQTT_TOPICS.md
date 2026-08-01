# Topik MQTT Tahaikal

## Subscribe dashboard

```text
tahaikal/device01/telemetry
tahaikal/device01/status
```

## Publish dashboard

```text
tahaikal/device01/control/mode/set
tahaikal/device01/control/pump1/set
tahaikal/device01/control/pump2/set
tahaikal/device01/control/thresholds/set
```

## Payload telemetry

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

## Payload kontrol

- Mode: `AUTO` atau `MANUAL`
- Pompa: `ON` atau `OFF`
- Thresholds: JSON dari form pengaturan dashboard
