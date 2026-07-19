# Business Platform

Экосистема сервисов для бизнеса в Тюмени. В production на VPS, реальные пользователи.

![Rust](https://img.shields.io/badge/Rust-1.96-orange?logo=rust)
![Vue](https://img.shields.io/badge/Vue-3-4FC08D?logo=vuedotjs)
![Kotlin](https://img.shields.io/badge/Kotlin-2.0-7F52FF?logo=kotlin)
![Android](https://img.shields.io/badge/Android-34-3DDC84?logo=android)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?logo=postgresql)
![Redis](https://img.shields.io/badge/Redis-7-DC382D?logo=redis)
![Nginx](https://img.shields.io/badge/Nginx-1.24-009639?logo=nginx)
![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04-E95420?logo=ubuntu)
![Zig](https://img.shields.io/badge/Zig-0.13-F7A41D?logo=zig)
![License](https://img.shields.io/badge/License-Apache%202.0-blue)

---

## Стек

| Слой | Технологии |
|------|-----------|
| **Бэкенд** | Rust (Axum 0.7, Tokio, SQLx, reqwest, tower-http, Redis) |
| **Фронтенды** | Vue 3 + TypeScript + Vite + Pinia |
| **Android** | Kotlin + Jetpack Compose + Hilt + Room + Retrofit + CameraX + ML Kit |
| **Mini Apps** | Vue 3 + VK Bridge / OK SDK / Telegram WebApp |
| **БД** | PostgreSQL 16 (schema-per-app — 13 схем) |
| **AI** | YandexGPT, GigaChat, Ollama |
| **Парсинг** | Rust (scraper, reqwest) |
| **Аудио** | Zig |
| **Сервер** | Ubuntu 24.04 + Nginx + systemd |

---

## Android

![Android](https://img.shields.io/badge/Kotlin-Compose%2BHilt%2BRoom-7F52FF?logo=kotlin)
![API](https://img.shields.io/badge/API-21%2B-3DDC84?logo=android)
![AutoUpdate](https://img.shields.io/badge/Auto--update-DownloadManager-blue)

Пять приложений, унифицированная архитектура: единый шаблон экранов (AuthScreen, FeedbackScreen, ChatScreen), общий модуль авто-обновления, сквозная тёмная тема.

### Работа
![APK](https://img.shields.io/badge/APK-21%20MB-lightgrey)
![WebSocket](https://img.shields.io/badge/Chat-WebSocket-blue)
![Search](https://img.shields.io/badge/Search-Fulltext-green)

Вакансии и резюме в Тюмени. Поиск по категориям, фильтр по зарплате/типу/формату работы, избранное, отклики, чат с работодателем, WebSocket.

### Объявления
![APK](https://img.shields.io/badge/APK-19%20MB-lightgrey)
![WebSocket](https://img.shields.io/badge/Chat-WebSocket-blue)
![Categories](https://img.shields.io/badge/Categories-Hierarchy-green)

Доска объявлений. Категории, поиск, фильтры, избранное, чат между продавцом и покупателем, WebSocket.

### Склад (WMS)
![APK](https://img.shields.io/badge/APK-8%20MB-lightgrey)
![ML Kit](https://img.shields.io/badge/Barcode-ML%20Kit-blue)
![1C](https://img.shields.io/badge/1C-Sync-green)

Складской учёт: номенклатура, приход, отгрузка, инвентаризация, перемещения, списания. Сканер штрихкодов через камеру (ML Kit). Синхронизация с 1С.

### Колл-центр
![APK](https://img.shields.io/badge/APK-17%20MB-lightgrey)
![Bitrix24](https://img.shields.io/badge/Bitrix24-REST%20API-blue)
![WebSocket](https://img.shields.io/badge/Chat-WebSocket-green)

Для операторов: звонки, клиенты, интеграция с Битрикс24 (контакты, сделки, активности). Чат с клиентами, WebSocket.

### Менеджер продаж
![APK](https://img.shields.io/badge/APK-129%20MB-lightgrey)
![Yandex Maps](https://img.shields.io/badge/Maps-Yandex-blue)
![Bitrix24](https://img.shields.io/badge/Bitrix24-REST%20API-green)

CRM для полевых менеджеров: клиенты, заказы, задачи, выездные встречи. Яндекс Карты для построения маршрутов. Интеграция с Битрикс24 и 1С.

---

## Vue PWA

![Vue](https://img.shields.io/badge/Vue-3-4FC08D?logo=vuedotjs)
![Pinia](https://img.shields.io/badge/State-Pinia-yellow)
![Router](https://img.shields.io/badge/Router-Vue%20Router-green)

### Панель управления (pwa-panel)
Административная панель: статистика, пользователи, обратная связь, настройки AI, управление агрегатором, мост сообщений. Лендинг платформы — `vsem72.ru`.

### Склад (warehouse-pwa)
Веб-версия WMS: товары, остатки, приход, отгрузка, инвентаризация, перемещение, списание. Для ПК без установки APK.

### Колл-центр (call-center)
Веб-версия колл-центра для операторов за ПК. Звонки, клиенты, чат.

### Менеджер продаж (manager-sales)
Веб-версия CRM. Клиенты, заказы, задачи. Для менеджеров за ПК.

---

## Mini Apps

![VK](https://img.shields.io/badge/VK-Mini%20App-0077FF?logo=vk)
![OK](https://img.shields.io/badge/OK-Mini%20App-ED8B00?logo=odnoklassniki)
![Telegram](https://img.shields.io/badge/Telegram-Mini%20App-26A5E4?logo=telegram)

Мини-приложения внутри соцсетей и мессенджеров. Открываются без установки — нажал и пользуешься.

| Платформа | Что есть | Технология |
|-----------|----------|-----------|
| **VK** | Объявления + Работа | Vue 3 + VK Bridge |
| **Одноклассники** | Объявления + Работа | Vue 3 + OK SDK |
| **Telegram** | Объявления + Работа | Vue 3 + Telegram WebApp |
| **Универсальные** | Объявления + Работа | VK Bridge (работает и в VK, и в OK) |

---

## Rust-сервисы (отдельные бинарники)

![Rust](https://img.shields.io/badge/Rust-Axum%200.7-orange?logo=rust)
![SQLx](https://img.shields.io/badge/DB-SQLx-blue)
![Tokio](https://img.shields.io/badge/Async-Tokio-green)

### api-gateway (порт 8080)
Единая точка входа для всех клиентов. Axum-сервер, JWT-авторизация, прокси дочерних сервисов, WebSocket для чатов. Вся бизнес-логика здесь.

### Агрегатор (aggregator, порт 8090)
![Parsing](https://img.shields.io/badge/Parsing-hh.ru%2FAvito%2FSuperJob%2FCIAN-blue)
![AI](https://img.shields.io/badge/AI-YandexGPT%2FGigaChat-green)

Парсинг внешних площадок: hh.ru, Avito, SuperJob, CIAN. Встроенные парсеры + универсальный HTML-парсер (CSS-селекторы) + парсер VK групп и Telegram каналов. AI-обогащение (YandexGPT/GigaChat). Авто-публикация в платформу.

### Пул агентов (agent-pool, порт 8091)
![AI](https://img.shields.io/badge/AI-Ollama-yellow)
![ZIP](https://img.shields.io/badge/Distribution-ZIP-blue)

Мультиагентная AI-платформа. Каждый домен — самодостаточный ZIP с ядром и промптами. Скачал → запустил → работает.

**Домены:**
- **Кодинг** — пишет код, рефакторит, проверяет безопасность, проектирует архитектуру, создаёт документацию
- **Коммуникации** — анализирует email, ведёт чаты, управляет уведомлениями
- **Офис** — планирует задачи, управляет календарём, создаёт документы, ведёт профили сотрудников

### Аудио-сервис (audio-service)
![Zig](https://img.shields.io/badge/Zig-0.13-F7A41D?logo=zig)
![STT](https://img.shields.io/badge/STT-SaluteSpeech-blue)
![TTS](https://img.shields.io/badge/TTS-GigaChat-green)

Zig-бинарник для распознавания речи (SaluteSpeech), синтеза (GigaChat) и анализа тональности. Общается с gateway через JSON по stdin/stdout.

---

## Интеграции

| Интеграция | Что делает |
|-----------|-----------|
| **Битрикс24** | Синхронизация контактов и сделок, добавление активностей звонка |
| **1С** | Синхронизация номенклатуры склада, выгрузка документов (приход/отгрузка) |
| **Telegram** | Боты для Mini Apps + Message Bridge (чат платформы ↔ Telegram) |
| **AI** | YandexGPT Lite, GigaChat (управляются через PWA) |
| **Т-Банк** | СБП, QR-коды для оплаты |

---

## База данных

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?logo=postgresql)
![Schema](https://img.shields.io/badge/Schemas-13-blue)

13 схем в PostgreSQL: `auth`, `ads`, `jobs`, `chat`, `feedback`, `payments`, `notifications`, `devops`, `admin`, `warehouse`, `integrations`, `bridge`, `communications`. Изоляция данных через schema-per-app.

---

## Инфраструктура

![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04-E95420?logo=ubuntu)
![Nginx](https://img.shields.io/badge/Nginx-1.24-009639?logo=nginx)
![Systemd](https://img.shields.io/badge/Systemd-Units-green)

```bash
systemctl status platform       # Rust API (порт 8080)
systemctl status aggregator     # Парсеры (порт 8090)
systemctl status agent-pool     # AI-агенты (порт 8091)
systemctl status nginx          # Веб-сервер
```

Production: `https://vsem72.ru/`

---

## Лицензия

Apache 2.0
