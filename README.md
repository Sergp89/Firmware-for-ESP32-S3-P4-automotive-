# 🚗 CarLink ESP Firmware

**Embedded firmware for automotive telemetry hub on ESP32-S3 / ESP32-P4**

[![ESP-IDF](https://img.shields.io/badge/ESP--IDF-v5.1+-blue?logo=espressif)](https://docs.espressif.com/projects/esp-idf/)
[![PlatformIO](https://img.shields.io/badge/PlatformIO-Compatible-orange?logo=platformio)](https://platformio.org/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Phase](https://img.shields.io/badge/Phase-1%20%7C%202%20Ready-purple)](../../releases)

---

## 📖 О проекте

**CarLink ESP Firmware** — это основная прошивка для бортового модуля сбора данных автомобиля. Управляет CAN-шиной, опрашивает датчики, ведёт логи на SD, взаимодействует с Android-приложением **CarLink ESP** и готов к миграции на ESP32-P4 для AI-задач.

Проект построен на **ESP-IDF (FreeRTOS)**, использует асинхронную архитектуру задач, аппаратный TWAI-контроллер и оптимизирован под работу в автомобильной среде (вибрация, перепады температур, помехи).

---

## 🎯 Возможности

### ✅ Phase 1 (ESP32-S3)
- [x] Аппаратная CAN-шина (TWAI, ISO 11898-1, 500 kbps)
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

## 🏗️ Архитектура прошивки
