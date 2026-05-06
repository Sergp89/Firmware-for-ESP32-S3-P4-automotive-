# 🚗 CarLink ESP Firmware

**Embedded firmware for automotive telemetry hub on ESP32-S3 / ESP32-P4**

[![ESP-IDF](https://img.shields.io/badge/ESP--IDF-v5.1+-blue?logo=espressif)](https://docs.espressif.com/projects/esp-idf/)
[![PlatformIO](https://img.shields.io/badge/PlatformIO-Compatible-orange?logo=platformio)](https://platformio.org/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Phase](https://img.shields.io/badge/Phase-1%20%7C%202%20Ready-purple)](../../releases)

---

## 📖 О проекте

**CarLink ESP Firmware** — это основная прошивка для бортового модуля сбора данных автомобиля. Управляет встроенной CAN-шиной (TWAI), подключается к внешним Bluetooth OBD2-сканерам, опрашивает датчики, ведёт логи на SD и синхронизируется с Android-приложением **CarLink ESP**.

Проект построен на **ESP-IDF (FreeRTOS)**, использует асинхронную многопоточную архитектуру, аппаратный TWAI-контроллер, стек Bluetooth Classic (SPP) + BLE (GATT) и оптимизирован под работу в автомобильной среде (вибрация, перепады температур, электромагнитные помехи).

---

## 🎯 Возможности

### ✅ Phase 1 (ESP32-S3)
- [x] Аппаратная CAN-шина (TWAI, ISO 11898-1, 500 kbps)
- [x] **Bluetooth OBD2-адаптеры**: ELM327, Rokidil ScanX, ScanTool Pro, VGate iCar и др.
- [x] Гибридный режим данных: внутренний TWAI + внешний OBD2 с автоматическим фолбэком
- [x] Опрос датчиков: BME680/BME280, BH1750, MQ-серия (ADC), GPS NEO-M8N
- [x] Управление RGB-подсветкой салона (WS2812B / SK6812)
- [x] Логирование на microSD (SPI, FATFS / LittleFS, ротация файлов)
- [x] Wi-Fi AP/STA режим + WebSocket сервер для Android-приложения
- [x] LVGL графический интерфейс на 4-5" SPI/RGB дисплее
- [x] Конфигурация через JSON API (без перепрошивки)

### 🚀 Phase 2 (ESP32-P4)
- [ ] MIPI-DSI дисплей 7" + аппаратный 2D-ускоритель PPA
- [ ] MIPI-CSI камера + ISP для детекции усталости водителя
- [ ] ESP-SR голосовое управление + шумоподавление
- [ ] Ethernet 100M + PoE (опционально)
- [ ] OTA-обновление прошивки через HTTPS

---

## 🔌 Поддержка Bluetooth OBD2

Проект нативно работает с большинством популярных Bluetooth OBD2-адаптеров, предоставляя гибкую альтернативу или дополнение к встроенной CAN-шине.

| Адаптер | Интерфейс | Поддержка |
|---------|-----------|-----------|
| **ELM327 (китайские аналоги)** | SPP (RFCOMM) / BLE | ✅ Полная (AT-инициализация + PID-опрос) |
| **Rokidil ScanX** | BLE GATT | ✅ Нативный профиль OBD2 |
| **ScanTool Pro / OBDLink** | SPP + BLE | ✅ Расширенные PID, быстрая частота опроса |
| **VGate iCar2/iCar3** | BLE | ✅ Автоматическое определение протокола |

**Как это работает:**
1. 📡 ESP32 сканирует пару устройств по имени/MAC или использует сохранённый профиль
2. 🔑 Устанавливает соединение SPP/BLE, проходит инициализацию (`ATZ`, `ATE0`, `ATSP0`, `0100`)
3. 🔄 Периодически запрашивает PID (`010C` RPM, `010D` Speed, `0105` Coolant, etc.)
4. 📊 Парсит HEX-ответы, конвертирует в SI-единицы, мержит с данными TWAI
5. ⚠️ При потере связи с CAN автоматически переключается на OBD2-поток

---

## 🏗️ Архитектура прошивки
┌─────────────────────────────────────────────────────────┐
│ ESP32-S3 / P4 │
├─────────────────────────────────────────────────────────┤
│ FreeRTOS Tasks (асинхронная многопоточность) │
│ ├─ can_task : Чтение/запись CAN-кадров (TWAI) │
│ ├─ bt_obd_task : Bluetooth SPP/BLE, AT-команды, PID │
│ ├─ sensor_task : Опрос I2C/ADC датчиков (1-2 Гц) │
│ ├─ gps_task : Парсинг NMEA по UART │
│ ├─ log_task : Запись на SD, ротация, flush │
│ ├─ display_task : LVGL рендеринг + тач-события │
│ ├─ wifi_task : WebSocket сервер, JSON API, mDNS │
│ └─ main_task : Инициализация, watchdog, конфиг │
├─────────────────────────────────────────────────────────┤
│ Inter-Task: Queues / EventBits | Config: NVS │
│ Protocol: JSON over WebSocket (ESP → App / App → ESP) │
└─────────────────────────────────────────────────────────┘

---

## 🔧 Технологический стек

| Компонент | Технология |
|-----------|------------|
| **SDK** | ESP-IDF v5.1+ / PlatformIO |
| **Язык** | C (основной), C++ (драйверы/обёртки) |
| **RTOS** | FreeRTOS (встроен в ESP-IDF) |
| **CAN** | `driver/twai.h` (аппаратный) |
| **Bluetooth** | `esp_bt.h` (SPP/RFCOMM) + `NimBLE` (GATT) |
| **OBD2** | Парсер ELM327 AT-ответов, OBD-II PID таблица |
| **Дисплей** | LVGL v8.3+ + `esp_lcd` + `esp_touch` |
| **Сеть** | `esp_http_server`, `esp_websocket_client`, mDNS |
| **Хранилище** | NVS (конфиг), FATFS / LittleFS (SD) |

---

## 📦 Структура проекта (ESP-IDF)

carlink-esp-fw/
├── CMakeLists.txt
├── sdkconfig.defaults
├── partitions.csv
├── main/
│ ├── CMakeLists.txt
│ ├── idf_component.yml
│ ├── main.c
│ ├── config.h
│ ├── tasks/
│ │ ├── can_task.c
│ │ ├── bt_obd_task.c 🔹 НОВОЕ
│ │ ├── sensor_task.c
│ │ ├── gps_task.c
│ │ ├── log_task.c
│ │ └── wifi_task.c
│ ├── drivers/
│ │ ├── twai_handler.c
│ │ ├── bme680_driver.c
│ │ ├── sd_card.c
│ │ └── elm327_parser.c 🔹 НОВОЕ
│ ├── ui/
│ │ ├── lvgl_init.c
│ │ └── screens/
│ └── utils/
│ └── json_parser.c
└── components/
