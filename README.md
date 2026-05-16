<div align="center">

# ⌚ GlucoVision Wearable Sync Service

**BLE smart band data collection and activity relay for health monitoring.**  
*BLE GATT · Heart rate · Step count · SpO2 · InfluxDB time-series · Flutter + Python*

[![Flutter](https://img.shields.io/badge/Flutter-BLE-02569B?style=for-the-badge&logo=flutter)](#)
[![FastAPI](https://img.shields.io/badge/FastAPI-Python-009688?style=for-the-badge&logo=fastapi)](#)
[![InfluxDB](https://img.shields.io/badge/InfluxDB-Time--Series-22ADF6?style=for-the-badge&logo=influxdb)](#)
[![Bluetooth](https://img.shields.io/badge/BLE-GATT-0082FC?style=for-the-badge&logo=bluetooth)](#)
[![Docker](https://img.shields.io/badge/Docker-Containerised-2496ED?style=for-the-badge&logo=docker)](#)
[![Status](https://img.shields.io/badge/Status-In%20Development-f59e0b?style=for-the-badge)](#)

</div>

---

## 📌 Purpose

GlucoVision Wearable Sync manages the entire lifecycle of connecting **BLE wearable devices** (smart bands / fitness watches) to the platform — pairing, bonding, reading health metrics (heart rate, steps, SpO2), and relaying real-time activity data to the recommendation and glucose prediction services.

> Separated from ESP32-CAM (`17`) because BLE protocol handling, GATT characteristic parsing, and activity data normalisation are a completely different domain from camera firmware and video streaming.

---

## 📁 Project Structure

```
18-glucovision-wearable-sync-service/
└── (Git repository initialised — structure to be scaffolded)
```

---

## ✨ Planned Features (by phase)

### Phase 1 — BLE Connection
- [ ] BLE scanning, connecting, and bonding (flutter_blue_plus)
- [ ] GATT characteristic parsing: HR (0x2A37), steps, battery (0x2A19)
- [ ] Device registry REST API
- [ ] Per-patient sync status API

### Phase 2 — Activity Relay
- [ ] Multi-band support: Xiaomi Mi Band, Fitbit, Garmin, generic HRS
- [ ] Activity normalisation to unified schema
- [ ] REST relay to `15` recommendation-engine and `12` glucose-prediction
- [ ] InfluxDB time-series storage

### Phase 3 — Real-time & Background
- [ ] WebSocket live HR stream during exercise
- [ ] Background BLE sync (Flutter WorkManager, every 5 min)
- [ ] HR zone classification (resting / fat-burn / cardio / peak)
- [ ] MQTT band telemetry (battery alerts)

---

## 🚀 Getting Started

### Prerequisites

- Flutter SDK ≥ 3.0 (for mobile BLE)
- Python ≥ 3.11 (for relay API)
- InfluxDB, MySQL, Docker & Docker Compose

### Mobile BLE (Flutter)

```bash
cd mobile_ble
flutter pub get
flutter run
```

### Relay API Server

```bash
cd relay_api
pip install -r requirements.txt
uvicorn main:app --reload --port 8013

docker compose up --build
```

---

## 🏗️ Planned Tech Stack

| Layer | Technology |
|---|---|
| BLE (Mobile) | Flutter `flutter_blue_plus` |
| BLE (Linux) | Python `bleak` library |
| GATT Parsing | Python `construct` (binary parsing) |
| REST API | FastAPI (Python) |
| WebSocket | FastAPI WebSocket (live HR) |
| Time-Series DB | InfluxDB |
| Database | MySQL (device registry, sync history) |
| Containerisation | Docker |

---

## 🔗 Backend Dependencies

| Service | Interaction |
|---|---|
| `05` api-gateway | REST routing + auth |
| `15` recommendation-engine | Activity data for TDEE + meal planning |
| `12` glucose-prediction | Activity as glucose forecast signal |
| `07` user-service | Patient device registration |
| InfluxDB | Activity time-series storage |

---

## 🔐 Security Notes

- BLE bonding with encrypted GATT (passkey or Just Works)
- HR, steps, SpO2 = health data — encrypted in InfluxDB at rest
- Device MAC addresses stored hashed — never in plaintext logs
- Only paired patient's band data accepted — unknown MACs rejected

---

## 📊 Unified Activity Schema

```json
{
  "user_id": "patient-uuid",
  "timestamp": "2026-05-10T08:30:00Z",
  "steps_total": 4200,
  "avg_heart_rate_bpm": 78,
  "hr_zone": "fat_burn",
  "spo2_pct": 97,
  "calories_kcal": 310,
  "active_minutes": 45,
  "source": "mi_band_8"
}
```

---

## 🧪 Testing (Planned)

```bash
pytest tests/gatt/         # Parse known binary packets → correct values
pytest tests/relay/        # POST activity → InfluxDB write verified
pytest tests/hr_zone/      # HR 145 → assert "cardio"
pytest tests/websocket/    # Live HR stable over 30-min session
pytest tests/sync/         # Band reconnect → gap backfill
```

---

<div align="center">

*Part of the [GlucoVision Platform](../01-glucovision-platform-architecture) — 21-Repo AI Diabetes Management System*

</div>
