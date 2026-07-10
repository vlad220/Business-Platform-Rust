# Business Platform Rust

Бизнес-платформа — экосистема сервисов для Тюмени: объявления, вакансии, складской учёт, колл-центр, CRM для менеджеров продаж, чаты, интеграции с 1С и Битрикс24.

---

## Архитектура

```
Android/Vue → Rust бэкенд (api-gateway, порт 8080) → PostgreSQL + Redis
                                                      → Bitrix24 REST API
                                                      → 1С API
                                                      → Telegram Bot API
                                                      → YandexGPT / GigaChat
```

**Бэкенд:** Rust (Axum + SQLx + Tokio) — микросервисная архитектура
**Фронтенды:** Vue 3 + TypeScript (PWA, VK Mini Apps, OK Mini Apps, Telegram Mini Apps)
**Мобильные:** Kotlin + Compose + Hilt/Room (5 Android-приложений)
**Инфраструктура:** Nginx, systemd, PostgreSQL 18, Redis

---

## Структура проекта

```
D:\business-platform-rust\
├── backend/                      # Rust workspace (весь бэкенд)
│   ├── Cargo.toml                # Workspace manifest
│   ├── api-gateway/              # Входная точка (Axum, порт 8080)
│   │   ├── src/
│   │   │   ├── main.rs
│   │   │   ├── routes/           # Маршруты по модулям
│   │   │   ├── middleware/       # Auth, CORS
│   │   │   └── error.rs          # ApiError
│   ├── services/                 # Независимые бинарные сервисы
│   │   ├── ads/
│   │   ├── auth/
│   │   ├── payments/
│   │   ├── notifications/
│   │   └── ...
│   ├── libs/                     # Shared crates (db, cache, shared)
│   └── migrations/               # SQL миграции (PostgreSQL)
├── pwa-panel/                    # Vue 3 + TS + Vite + Pinia (порт 5173)
├── vk-ads/                       # VK Mini App «Объявления»
├── vk-rabota/                    # VK Mini App «Работа»
├── ok-ads/                       # OK Mini App «Объявления»
├── ok-rabota/                    # OK Mini App «Работа»
├── tg-ads/                       # Telegram Mini App «Объявления»
├── tg-rabota/                    # Telegram Mini App «Работа»
├── vk-bridge-ads/                # Универсальное Mini App (VK + OK) «Объявления»
├── vk-bridge-rabota/             # Универсальное Mini App (VK + OK) «Работа»
├── Работа/                       # Android app (Kotlin + Compose + Hilt + Room)
├── Объявления/                   # Android app (Kotlin + Compose + Hilt + Room)
├── call-center/                  # Vue PWA «Колл-центр» (порт 3011)
├── call-center-kotlin/           # Android «Колл-центр»
├── manager-sales/                # Vue PWA «Менеджер продаж» (порт 3010)
├── manager-sales-kotlin/         # Android «Менеджер продаж»
├── warehouse-pwa/                # Vue PWA «Склад» (порт 3020)
├── warehouse-kotlin/             # Android «Склад» (сканер ШК + WMS)
├── aggregator/                   # Микросервис агрегатора объявлений (порт 8090)
├── audio-service/                # Zig-сервис обработки аудио
├── infra/                        # Nginx, systemd, backup, deploy
│   ├── nginx/                    # Конфиги Nginx
│   ├── scripts/                  # Скрипты деплоя
│   └── systemd/                  # systemd unit-файлы
├── docs/                         # Документация
├── mani/                         # Монетизация
├── sbp/                          # СБП (Т-Банк API)
├── Ошибки и предложения/         # JSONL-файлы обратной связи
├── Иконки/                       # Иконки приложений
├── run.ps1                       # Запуск dev-сервера (Windows)
└── deploy.ps1                    # Интерактивный деплой на VPS
```

---

## Быстрый старт (локальная разработка)

### Требования

- **Rust** (установить через [rustup.rs](https://rustup.rs/))
- **PostgreSQL 16+** (БД )
- **Redis** (порт )
- **Node.js 20+** (для фронтендов)

### Запуск

```powershell
# Всё одной командой
.\run.ps1

# Или пошагово:
cd backend
cargo run --bin api-gateway
```

### Фронтенды

```powershell
# PWA панель
cd pwa-panel
npm install
npm run dev          # → http://localhost:5173/

# Остальные PWA (каждый в своём терминале)
cd call-center       && npm run dev   # порт 3011
cd manager-sales     && npm run dev   # порт 3010
cd warehouse-pwa     && npm run dev   # порт 3020
cd vk-ads            && npm run dev   # порт 3001
cd vk-rabota         && npm run dev   # порт 3003
cd ok-ads            && npm run dev   # порт 3002
cd tg-ads            && npm run dev   # порт 3007
cd tg-rabota         && npm run dev   # порт 3008
```

---

## API Endpoints

### Auth
| Method | Endpoint | Описание |
|--------|----------|---------|
| POST | `/api/v1/auth/register` | Регистрация |
| POST | `/api/v1/auth/login` | Вход (email или телефон) |
| POST | `/api/v1/auth/logout` | Выход |
| POST | `/api/v1/auth/refresh` | Обновление токена |
| POST | `/api/v1/auth/oauth/yandex` | OAuth Яндекс |
| POST | `/api/v1/auth/oauth/vk` | OAuth ВКонтакте |
| POST | `/api/v1/auth/oauth/ok` | OAuth Одноклассники |
| POST | `/api/v1/auth/send-code` | Отправить SMS-код |
| POST | `/api/v1/auth/verify-code` | Подтвердить SMS-код |
| GET | `/api/v1/auth/referral/stats` | Реферальная статистика |

### Ads (Объявления)
| Method | Endpoint | Описание |
|--------|----------|---------|
| GET | `/api/v1/ads/categories` | Категории |
| GET | `/api/v1/ads/listings` | Список объявлений |
| POST | `/api/v1/ads/listings` | Создать объявление |
| GET | `/api/v1/ads/listings/{id}` | Детали объявления |

### Jobs (Вакансии)
| Method | Endpoint | Описание |
|--------|----------|---------|
| GET | `/api/v1/jobs/categories` | Категории |
| GET | `/api/v1/jobs/listings` | Список вакансий |
| POST | `/api/v1/jobs/listings` | Создать вакансию |
| GET | `/api/v1/jobs/listings/{id}` | Детали вакансии |

### Chat
| Method | Endpoint | Описание |
|--------|----------|---------|
| GET | `/api/v1/chat/conversations` | Список бесед |
| GET | `/api/v1/chat/messages/{conv_id}` | Сообщения |
| POST | `/api/v1/chat/messages/{conv_id}` | Отправить сообщение |

### Warehouse (Склад WMS)
| Method | Endpoint | Описание |
|--------|----------|---------|
| GET/POST | `/api/v1/warehouse/products` | Товары |
| GET | `/api/v1/warehouse/products/by-barcode/{barcode}` | Поиск по ШК |
| POST | `/api/v1/warehouse/products/sync` | Синхронизация с 1С |
| GET/POST | `/api/v1/warehouse/receipts` | Приходы |
| GET/POST | `/api/v1/warehouse/shipments` | Отгрузки |
| GET/POST | `/api/v1/warehouse/inventories` | Инвентаризации |
| GET/POST | `/api/v1/warehouse/moves` | Перемещения |
| GET/POST | `/api/v1/warehouse/write-offs` | Списания |

### Audio
| Method | Endpoint | Описание |
|--------|----------|---------|
| POST | `/api/v1/audio/transcribe` | Распознавание речи |
| POST | `/api/v1/audio/analyze` | Анализ текста |
| POST | `/api/v1/audio/synthesize` | Синтез речи |
| GET | `/api/v1/audio/health` | Health check |

### Integrations
| Method | Endpoint | Описание |
|--------|----------|---------|
| GET/POST | `/api/v1/integrations/bitrix24/*` | Интеграция с Битрикс24 |
| GET/POST | `/api/v1/integrations/onec/*` | Интеграция с 1С |

### Bridge (Мост сообщений)
| Method | Endpoint | Описание |
|--------|----------|---------|
| POST | `/api/v1/bridge/telegram/webhook` | Webhook от Telegram |
| POST | `/api/v1/bridge/whatsapp/webhook` | Webhook от WhatsApp |
| GET/POST/DELETE | `/api/v1/bridge/links` | Управление привязками |

### Admin
| Method | Endpoint | Описание |
|--------|----------|---------|
| GET | `/api/v1/admin/stats` | Статистика |
| GET | `/api/v1/admin/users` | Пользователи |
| GET/PUT | `/api/v1/admin/feedback/{id}` | Обратная связь |
| GET/PUT | `/api/v1/admin/ai/settings` | Настройки AI |
| GET/PUT | `/api/v1/admin/bridge/settings` | Настройки моста |

### Releases
| Method | Endpoint | Описание |
|--------|----------|---------|
| GET | `/api/v1/releases/latest/{app}` | Последняя версия APK |
| GET | `/api/v1/releases/download/{app}/{file}` | Скачать APK |

---

## Production (VPS)

**Сервер:** 
**Домен:** `https://vsem72.ru/`

### SSH
```bash

```

### Управление сервисами
```bash
systemctl status platform       # Rust API (порт 8080)
systemctl status aggregator     # Агрегатор (порт 8090)
systemctl status nginx          # Веб-сервер
journalctl -u platform -f       # Логи бэкенда
```

### PostgreSQL
```bash
# Подключение
sudo -u postgres psql -d 

# SSH-туннель (с Windows)
ssh 
# → localhost:5433
```

### Деплой
```powershell
# Интерактивный деплой
.\deploy.ps1

# Или через run.ps1
.\run.ps1 -DeployBackend    # Только Rust бэкенд
.\run.ps1 -DeployFull       # Полный деплой
.\run.ps1 -DeployPanel      # Только PWA панель
.\run.ps1 -DeployVkAds      # VK Mini App
.\run.ps1 -DeployTg         # Telegram Mini Apps
.\run.ps1 -SyncFeedback     # Синхронизация JSONL
```

---

## Android-приложения

| Приложение | Папка исходников | APK | Технологии |
|-----------|-----------------|-----|------------|
| Работа | `D:\business-platform\котлин работа\` | `Работа.apk` (~21 MB) | Kotlin + Compose + Hilt + Room |
| Объявления | `D:\business-platform\Объявления\` | `Объявления.apk` (~19 MB) | Kotlin + Compose + Hilt + Room |
| Колл-центр | `call-center-kotlin\` | `Колл-центр.apk` (~17 MB) | Kotlin + Compose + Hilt + Room |
| Менеджер продаж | `manager-sales-kotlin\` | `Менеджер продаж.apk` (~129 MB) | Kotlin + Compose + Hilt + Room + Яндекс Карты |
| Склад | `warehouse-kotlin\` | `Склад.apk` (~8 MB) | Kotlin + Compose + CameraX + ML Kit |

### Сборка APK
```powershell
$env:JAVA_HOME = "D:\jdk-21\jdk-21"
cd D:\business-platform\котлин работа
.\gradlew.bat assembleDebug
```

### Auto-update
При запуске приложение проверяет `GET /api/v1/releases/latest/{app}` — если есть новая версия, предлагает обновление.

---

## Mini Apps

| Платформа | Объявления | Работа |
|-----------|-----------|--------|
| **VK** | `https://vsem72.ru/vk-ads/` (App ID: 54664923) | `https://vsem72.ru/vk-rabota/` (App ID: 54665028) |
| **OK** | `https://vsem72.ru/ok-ads/` | `https://vsem72.ru/ok-rabota/` |
| **Telegram** | `https://vsem72.ru/tg-ads/` (@vsem_ads_bot) | `https://vsem72.ru/tg-rabota/` (@tyumen_jobs_bot) |
| **VK Bridge** | `https://vsem72.ru/vk-bridge-ads/` | `https://vsem72.ru/vk-bridge-rabota/` |

---

## База данных (PostgreSQL)

**Схемы (schema-per-app):**
- `auth` — пользователи, сессии, токены
- `ads` — категории, объявления, медиа
- `jobs` — вакансии, резюме, отклики
- `chat` — беседы, сообщения, участники
- `feedback` — отзывы, жалобы, обращения
- `payments` — транзакции, статусы
- `notifications` — уведомления
- `devops` — релизы, метрики
- `admin` — администраторы, права
- `warehouse` — складской учёт
- `integrations` — интеграции (Bitrix24)
- `bridge` — мост сообщений
- `communications` — коммуникации

---

## Интеграции

### Битрикс24
- **Подход:** исходящий вебхук (пользователь вставляет URL) → прокси через Rust
- **Эндпоинты:** connect, status, disconnect, sync, activities
- **OAuth 2.0:** запланирован (для подключения в один клик)

### 1С
- Синхронизация номенклатуры склада
- Выгрузка документов (приход, отгрузка)

### Telegram
- Боты: `@vsem_ads_bot`, `@tyumen_jobs_bot`
- Message Bridge: двусторонняя переписка между чатами платформы и Telegram

### AI
- YandexGPT Lite (1000 запросов/день)
- GigaChat (100 запросов/день)
- Управление через PWA панель `/panel/admin/ai`

---

## Монетизация

- **Схема:** реквизиты в PWA → клиент переводит сам → кнопка «Я оплатил»
- **СБП:** Т-Банк API (QR-код)
- **Цены:** настраиваются админом в PWA

---

## Важные правила

1. **Docker запрещён** — не использовать, не предлагать
2. **Schema-per-app** — изоляция данных через схемы PostgreSQL
3. **Thin client** — Android только UI + Room-кэш, вся логика на бэкенде
4. **Только Тюмень** — все сервисы ориентированы на Тюмень
5. **Русский язык** — весь код, комментарии, документация на русском
6. **versionCode** — увеличивать на +1 перед каждой сборкой APK

---

## Лицензия

© 2026 Business Platform. Все права защищены.
