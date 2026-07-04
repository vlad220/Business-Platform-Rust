# Business-Platform-Rust
Объявления и работа
# Business Platform Rust— Инструкция для AI-агентов (Rust)

## Язык
Все ответы, комментарии в коде, документация — **только русский**. Пользователь не понимает английский.  
**Никогда** не используй hardcoded строки на русском в коде — используй string resources / локализацию. Эталонная локаль — `ru`.  
**Docker**: НЕ использовать, НЕ предлагать (забанено пользователем).

## Документация: только AGENTS.md

**НЕ создавай** новые файлы документации (.md), README, инструкций, текстовые файлы, info-файлы и т.д.

Вся документация, заметки, конфигурация, история изменений — только в этом файле **AGENTS.md**.  
Обновляй AGENTS.md при любом изменении проекта.

### Файлы пользователя — НЕ ТРОГАТЬ, НЕ УДАЛЯТЬ НИКОГДА:
- `сети.txt` — контакты и ссылки пользователя
- `business-platform-rust.txt` — личные заметки пользователя
-  `robokassaсоц.txt` — монетизация
Эти файлы созданы пользователем вручную, их **нельзя** стирать, переименовывать, редактировать или перемещать.

---

## Структура проекта

```
D:\business-platform-rust\
├── backend/                      # Rust workspace (весь бэкенд)
│   ├── Cargo.toml                # Workspace manifest
│   ├── api-gateway/              # Входная точка (Axum, порт 8080)
│   │   ├── src/
│   │   │   ├── main.rs
│   │   │   ├── routes/           # Маршруты
│   │   │   ├── middleware/       # Auth, CORS
│   │   │   └── error.rs          # ApiError
│   ├── services/                 # Независимые бинарные сервисы
│   │   ├── ads/
│   │   ├── auth/
│   │   ├── payments/
│   │   ├── notifications/
│   │   └── ...
│   ├── libs/                     # Shared crates
│   │   ├── db/
│   │   ├── cache/
│   │   └── shared/
│   └── migrations/               # SQL миграции (postgres)
├── pwa-panel/                    # Vue 3 + TS + Vite + Pinia (порт dev: 5173)
├── vk-ads/                       # VK Mini App «Объявления» (Vue 3 + Vite + VK Bridge)
├── vk-rabota/                    # VK Mini App «Работа» (Vue 3 + Vite + VK Bridge)
├── ok-ads/                       # OK Mini App «Объявления» (Vue 3 + Vite + OK SDK)
├── Работа/                       # Android app (Kotlin + Compose + Hilt + Room)
├── Объявления/                   # Android app (Kotlin + Compose + Hilt + Room)
├── infra/                        # Nginx, systemd, backup, deploy
├── docs/                         # Документация
└── run.ps1                       # Запуск dev-сервера (Windows)
```

---

## Dev-сервер (Windows)

Запуск: `.\run.ps1` (проверяет Rust, PG, env, билдит, запускает)
**Проверки:** Rust toolchain, PostgreSQL connectivity, .env vars, SQLx prepare, билд
**Папки:** `backend/`, `Ошибки и предложения/` (JSONL)

**Новые возможности скрипта:**
- `.\run.ps1 -DeployFull` — полный деплой проекта на VPS
- `.\run.ps1 -SyncFeedback` — синхронизация JSONL-файлов с VPS

**Проверки зависимостей перед деплоем:**
- Для `-DeployFull` скрипт проверяет наличие всех необходимых инструментов: Rust/Cargo, Node.js/npm, SSH/SCP, tar
- Проверяет наличие всех требуемых файлов проекта: `backend/Cargo.toml`, `pwa-panel/package.json`
- Проверяет подключение к VPS перед началом операций

---

## API Endpoints

### Auth
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| POST | `/api/v1/auth/register` | Регистрация | ❌ | ✅ |
| POST | `/api/v1/auth/login` | Логин | ❌ | ✅ |
| POST | `/api/v1/auth/logout` | Выход | ✅ | ❌ |
| POST | `/api/v1/auth/refresh` | Обновление токена | ❌ | ✅ |
| POST | `/api/v1/auth/oauth/yandex` | OAuth Яндекс | ❌ | ✅ |
| POST | `/api/v1/auth/oauth/google` | OAuth Google | ❌ | ✅ |
| POST | `/api/v1/auth/oauth/vk` | OAuth ВКонтакте | ❌ | ✅ |
| POST | `/api/v1/auth/oauth/ok` | OAuth Одноклассники | ❌ | ✅ |

### Ads
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| GET | `/api/v1/ads/categories` | Категории объявлений | ❌ | ✅ |
| GET | `/api/v1/ads/listings` | Объявления | ❌ | ✅ |
| POST | `/api/v1/ads/listings` | Создать объявление | ✅ | ❌ |
| GET | `/api/v1/ads/listings/{id}` | Объявление | ❌ | ✅ |

### Jobs
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| GET | `/api/v1/jobs/categories` | Категории вакансий | ❌ | ✅ |
| GET | `/api/v1/jobs/listings` | Вакансии | ❌ | ✅ |
| POST | `/api/v1/jobs/listings` | Создать вакансию | ✅ | ❌ |
| GET | `/api/v1/jobs/listings/{id}` | Вакансия | ❌ | ✅ |

### Payments
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| POST | `/api/v1/payments/create` | Создать платёж (возвращает URL Robokassa) | ✅ | ❌ |
| POST | `/api/v1/payments/webhook` | Уведомление от Robokassa | ❌ | ✅ |
| GET | `/api/v1/payments/status/{payment_id}` | Статус платежа | ✅ | ❌ |

### Chat
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| GET | `/api/v1/chat/conversations` | Беседы пользователя | ✅ | ❌ |
| GET | `/api/v1/chat/messages/{conv_id}` | Сообщения в беседе | ✅ | ❌ |
| POST | `/api/v1/chat/messages/{conv_id}` | Отправить сообщение | ✅ | ❌ |
| GET | `/api/v1/chat/messages/{conv_id}/unread` | Непрочитанные | ✅ | ❌ |

### Notifications
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| GET | `/api/v1/notifications` | Уведомления | ✅ | ❌ |
| POST | `/api/v1/notifications/read` | Отметить прочитанным | ✅ | ❌ |
| GET | `/api/v1/notifications/unread-count` | Кол-во непрочитанных | ✅ | ❌ |

### Admin
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| GET | `/api/v1/admin/stats` | Статистика (users, ads, jobs, downloads, feedback) | ✅ | ❌ |
| GET | `/api/v1/admin/users` | Пользователи | ✅ | ❌ |
| GET | `/api/v1/admin/feedback` | Обратная связь | ✅ | ❌ |
| PUT | `/api/v1/admin/feedback/{id}` | Обновить статус обратной связи | ✅ | ❌ |
| GET | `/api/v1/admin/payments` | Платежи | ✅ | ❌ |
| PUT | `/api/v1/admin/payments/{id}` | Обновить статус платежа | ✅ | ❌ |

### Releases
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| GET | `/api/v1/releases/latest/{app}` | Последний релиз | ❌ | ✅ |
| GET | `/api/v1/releases/download/{app}/{file}` | Скачать APK | ❌ | ✅ |

---

## VPS (Production)

IP: `185.221.214.239` (публичный IP, не Cloudflare)

### SSH-доступ
```
# Подключение
ssh root@185.221.214.239

# SSH-туннель (для локального доступа к БД)
# Прокинуть порт 5433 → VPS:5432
ssh -L 5433:127.0.0.1:5432 root@185.221.214.239
```

### Управление сервисами (на VPS)
```
systemctl start|stop|restart|status business-platform
systemctl start|stop|restart|status pwa-panel
systemctl start|stop|restart|status nginx
journalctl -u business-platform -f
```

> **⚠️ ВАЖНО:** Windows → Linux кросс-компиляция не настроена. Сборка только на VPS.

```bash
# 1. Скопировать исходники на VPS
scp -r .cargo api-gateway libs migrations services Cargo.toml Cargo.lock root@185.221.214.239:/opt/platform/src/

# 2. Собрать на VPS
ssh root@185.221.214.239 "export HOME=/root; source $HOME/.cargo/env; cd /opt/platform/src && cargo build --release --bin api-gateway"

# 3. Заменить бинарник и перезапустить
ssh root@185.221.214.239 "cp /opt/platform/src/target/release/api-gateway /opt/platform/api-gateway && chmod +x /opt/platform/api-gateway && systemctl restart platform"
```

### Параметры VPS
| Item | Value |
|------|-------|
| IP | `185.221.214.239` |
| SSH | `root@185.221.214.239` / пароль: `EK1pqJ6fi6` |
| API | `http://185.221.214.239:8080` |

### Безопасность VPS (применено 2026-07-02)
- **Firewall**: `ufw allow 22,80,443/tcp; ufw enable`
- **SSH**: ⚠️ `PermitRootLogin yes` + пароль (пока не настроен ключ)
- **SSL**: certbot-auto для `vsem72.ru`, автопродление
- **Nginx**: reverse proxy, rate limiting, CORS, gzip
- **Backup**: ежедневно в `backup/`, удаленно (не на этом VPS)

### PostgreSQL на VPS
- Host: `127.0.0.1` / Port: `5432` (на самом VPS)
- Host: `localhost` / Port: `5433` (через SSH-туннель с Windows)
- User: `baza220_user` / Pass: `ZAZ968laga`
- DB: `baza220`

**Команды для работы (на VPS):**
```bash
sudo -u postgres psql -d baza220
pg_dump -U baza220_user -d baza220 > backup.sql
psql -U baza220_user -d baza220 -f backup.sql
```

---

## Android Apps (Kotlin)

### Работа (`Работа/`)
- **Технологии**: Kotlin, Compose, Hilt, Room, Coroutines, Retrofit
- **Версия API**: 21+ (Android 5.0+)
- **Назначение**: приложение для поиска работы и вакансий
- **Структура**: `app/src/main/java/com/baza220/jobs/`

#### Особенности
- **Кэш**: Room БД (SQLite) для offline режима
- **DI**: Hilt (KSP)
- **Networking**: Retrofit + OkHttp + Gson
- **Compose navigation**: NavGraph с bottom bar
- **Auth**: JWT токены в SharedPreferences

#### Сборка
- **Версия Kotlin**: 2.0.0+
- **Compose Compiler**: 1.5.0+
- **AGP**: 8.1.4+
- **Gradle**: 8.5 (wrapper)

#### Подписание APK
- **Keystore**: `keystore.jks` (в корне проекта)
- **Password**: в `local.properties` (gitignored)
- **Alias**: `key0`

#### Splash screen
- **API**: Android 12+ (SDK 31+)
- **Библиотека**: `androidx.core:core-splashscreen`
- **Тема**: `Theme.SplashScreen` с брендированным фоном

### Объявления (`Объявления/`)
- **Технологии**: Kotlin, Compose, Hilt, Room, Coroutines, Retrofit
- **Версия API**: 21+ (Android 5.0+)
- **Назначение**: приложение для размещения и поиска объявлений
- **Структура**: `app/src/main/java/com/baza220/ads/`

#### Особенности
- **Кэш**: Room БД (SQLite) для offline режима
- **DI**: Hilt (KSP)
- **Networking**: Retrofit + OkHttp + Gson
- **Compose navigation**: NavGraph с bottom bar
- **Auth**: JWT токены в SharedPreferences

#### Сборка
- **Версия Kotlin**: 2.0.0+
- **Compose Compiler**: 1.5.0+
- **AGP**: 8.1.4+
- **Gradle**: 8.5 (wrapper)

#### Подписание APK
- **Keystore**: `keystore.jks` (в корне проекта)
- **Password**: в `local.properties` (gitignored)
- **Alias**: `key0`

#### Splash screen
- **API**: Android 12+ (SDK 31+)
- **Библиотека**: `androidx.core:core-splashscreen`
- **Тема**: `Theme.SplashScreen` с брендированным фоном

---

## APK Distribution

| App | Path (VPS) | Endpoint | Size |
|-----|------------|----------|------|
| Работа | `/var/www/apk/jobs/latest.apk` | `/api/v1/releases/download/jobs/latest.apk` | ~20MB |
| Объявления | `/var/www/apk/ads/latest.apk` | `/api/v1/releases/download/ads/latest.apk` | ~19MB |

**Загрузка новых APK:**
```bash
# Работа
scp .../Работа-debug.apk root@185.221.214.239:/var/www/apk/jobs/latest.apk

# Объявления
scp .../app-debug.apk root@185.221.214.239:/var/www/apk/ads/latest.apk
```

---

## Database Schema (PostgreSQL)

### Схемы
- `auth` — пользователи, сессии, токены
- `ads` — категории, объявления, медиа
- `jobs` — вакансии, резюме, отклики
- `chat` — беседы, сообщения, участники
- `feedback` — отзывы, жалобы, обращения
- `payments` — транзакции, статусы, подписки
- `notifications` — уведомления, логи
- `devops` — релизы, метрики, логи
- `admin` — администраторы, права

### auth.users
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
email VARCHAR(255) UNIQUE NOT NULL
phone VARCHAR(20) UNIQUE
password_hash TEXT NOT NULL
first_name VARCHAR(100)
last_name VARCHAR(100)
avatar_url TEXT
is_active BOOLEAN DEFAULT true
is_admin BOOLEAN DEFAULT false
created_at TIMESTAMPTZ DEFAULT NOW()
updated_at TIMESTAMPTZ DEFAULT NOW()
```

### auth.sessions
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE
token_hash TEXT UNIQUE NOT NULL
expires_at TIMESTAMPTZ NOT NULL
created_at TIMESTAMPTZ DEFAULT NOW()
```

### auth.subscriptions
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE
plan VARCHAR(50) NOT NULL  -- 'basic', 'premium', 'pro'
status VARCHAR(20) NOT NULL  -- 'active', 'expired', 'cancelled'
amount DECIMAL(10,2)
expires_at TIMESTAMPTZ
created_at TIMESTAMPTZ DEFAULT NOW()
```

### ads.categories
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
name VARCHAR(100) NOT NULL
description TEXT
parent_id UUID REFERENCES ads.categories(id)
icon TEXT
sort_order INT DEFAULT 0
created_at TIMESTAMPTZ DEFAULT NOW()
```

### ads.listings
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE
category_id UUID REFERENCES ads.categories(id)
title VARCHAR(255) NOT NULL
description TEXT
price DECIMAL(12,2)
city VARCHAR(100) DEFAULT 'Тюмень'
address TEXT
lat DECIMAL(10,8)
lon DECIMAL(11,8)
status VARCHAR(20) DEFAULT 'active'  -- 'active', 'inactive', 'sold'
views_count INT DEFAULT 0
created_at TIMESTAMPTZ DEFAULT NOW()
updated_at TIMESTAMPTZ DEFAULT NOW()
```

### jobs.categories
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
name VARCHAR(100) NOT NULL
description TEXT
parent_id UUID REFERENCES jobs.categories(id)
icon TEXT
sort_order INT DEFAULT 0
created_at TIMESTAMPTZ DEFAULT NOW()
```

### jobs.listings
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE
category_id UUID REFERENCES jobs.categories(id)
title VARCHAR(255) NOT NULL
description TEXT
salary_from DECIMAL(10,2)
salary_to DECIMAL(10,2)
employment_type VARCHAR(50)  -- 'full_time', 'part_time', 'contract', 'internship'
work_format VARCHAR(50)  -- 'office', 'remote', 'hybrid'
city VARCHAR(100) DEFAULT 'Тюмень'
address TEXT
lat DECIMAL(10,8)
lon DECIMAL(11,8)
status VARCHAR(20) DEFAULT 'active'  -- 'active', 'inactive', 'filled'
views_count INT DEFAULT 0
created_at TIMESTAMPTZ DEFAULT NOW()
updated_at TIMESTAMPTZ DEFAULT NOW()
```

### chat.conversations
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
name VARCHAR(255)
type VARCHAR(20) DEFAULT 'private'  -- 'private', 'group'
created_at TIMESTAMPTZ DEFAULT NOW()
```

### chat.conversation_members
```sql
conversation_id UUID REFERENCES chat.conversations(id) ON DELETE CASCADE
user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE
joined_at TIMESTAMPTZ DEFAULT NOW()
PRIMARY KEY (conversation_id, user_id)
```

### chat.messages
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
conversation_id UUID REFERENCES chat.conversations(id) ON DELETE CASCADE
sender_id UUID REFERENCES auth.users(id) ON DELETE CASCADE
content TEXT NOT NULL
status VARCHAR(20) DEFAULT 'sent'  -- 'sent', 'delivered', 'read'
created_at TIMESTAMPTZ DEFAULT NOW()
```

### payments.transactions (фактическая таблица)
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
user_id UUID NOT NULL REFERENCES auth.users(id)
amount INT NOT NULL
currency VARCHAR(3) DEFAULT 'RUB'
status VARCHAR(20) DEFAULT 'pending'  -- 'pending', 'succeeded', 'failed', 'cancelled'
yookassa_payment_id VARCHAR(255)  -- InvId от Robokassa (исторически yookassa_)
yookassa_status VARCHAR(50)  -- статус от провайдера
description TEXT
created_at TIMESTAMPTZ DEFAULT NOW()
updated_at TIMESTAMPTZ DEFAULT NOW()
```

### notifications.notifications
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE
title VARCHAR(255) NOT NULL
content TEXT NOT NULL
type VARCHAR(50) NOT NULL  -- 'info', 'warning', 'error', 'promotion'
is_read BOOLEAN DEFAULT false
created_at TIMESTAMPTZ DEFAULT NOW()
```

### devops.releases
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
app VARCHAR(50) NOT NULL  -- 'jobs', 'ads', 'pwa'
version VARCHAR(20) NOT NULL
version_code INT NOT NULL
file_name VARCHAR(255) NOT NULL
file_size BIGINT NOT NULL
changelog TEXT
is_latest BOOLEAN DEFAULT false
created_at TIMESTAMPTZ DEFAULT NOW()
```

### feedback.submissions
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
app VARCHAR(50) NOT NULL  -- 'jobs', 'ads', 'pwa', 'работа', 'объявления'
type VARCHAR(50) NOT NULL  -- 'bug', 'feature_request', 'complaint', 'praise'
title VARCHAR(255) NOT NULL
description TEXT
status VARCHAR(20) DEFAULT 'new'  -- 'new', 'in_progress', 'resolved', 'rejected'
created_at TIMESTAMPTZ DEFAULT NOW()
updated_at TIMESTAMPTZ DEFAULT NOW()
```

---

## OAuth Providers

### Яндекс
- **Кабинет**: https://oauth.yandex.ru/
- **Тип приложения**: Web-приложение
- **Callback URL**: `https://vsem72.ru/api/v1/auth/oauth/yandex/callback`
- **Scope**: `login:info login:email`

### Google
- **Кабинет**: https://console.cloud.google.com/
- **Тип приложения**: Веб-приложение
- **Callback URL**: `https://vsem72.ru/api/v1/auth/oauth/google/callback`
- **Scope**: `openid email profile`

---

## Frontend Apps

### PWA Panel (`pwa-panel/`)
- **Технологии**: Vue 3, TypeScript, Vite, Pinia, Tailwind CSS
- **Порт разработки**: 5173
- **Назначение**: административная панель и пользовательский интерфейс

#### Особенности
- **Routing**: Vue Router
- **State**: Pinia store
- **Styling**: Tailwind CSS + custom components
- **API**: axios
- **Auth**: JWT токены через localStorage

#### Страницы
- `/` — главная
- `/login` — вход
- `/register` — регистрация
- `/dashboard` — дашборд
- `/ads` — объявления
- `/jobs` — вакансии
- `/chat` — чат
- `/feedback` — отзывы
- `/analytics` — аналитика
- `/users` — пользователи (админ)
- `/admin` — администрирование (админ)

### VK Mini App Работа (`vk-rabota/`)
- **Технологии**: Vue 3, TypeScript, Vite, VK Bridge, Pinia
- **Порт разработки**: 3003
- **Назначение**: мини-приложение ВКонтакте для вакансий
- **Расположение на сервере**: `location /vk-rabota/`
- **base path**: `/vk-rabota/` (задан в `vite.config.ts` и `index.html`)
- **VK App ID**: через `VITE_VK_APP_ID` в `vite.config.ts` (по умолчанию `'0'` — заменить перед деплоем)

#### Страницы
- `/` — лента вакансий (поиск, фильтр по категории, сортировка по зарплате/дате)
- `/create` — создание вакансии (название, описание, зарплата, тип, формат)
- `/profile` — профиль
- `/favorites` — избранное
- `/vacancy/:id` — детали вакансии

#### Особенности
- VK Bridge: `VKWebAppGetAuthToken` для авторизации
- API: `GET /jobs/vacancies`, `POST /jobs/vacancies`, `GET /jobs/favorites`, etc.
- Модель вакансии: title, description, salary_from, salary_to, employment_type, work_format, city, category_id
- База: скопирована из vk-ads, переделана под jobs API

### VK Mini App Объявления (`vk-ads/`)
- **Технологии**: Vue 3, TypeScript, Vite, VK Bridge, Pinia
- **Порт разработки**: 3001
- **Назначение**: мини-приложение ВКонтакте для объявлений
- **Расположение на сервере**: `location /vk-ads/`
- **base path**: `/vk-ads/` (задан в `vite.config.ts` и `index.html`)
- **VK App ID**: через `VITE_VK_APP_ID` в `vite.config.ts` (по умолчанию `'0'` — заменить перед деплоем)

#### Особенности
- **VK Bridge**: взаимодействие с API ВКонтакте (`VKWebAppGetUserInfo`, `VKWebAppGetAuthToken`)
- **Auth**: через VK ID — получает токен VK → отправляет на `POST /api/v1/auth/oauth/vk` → получает JWT
- **API**: те же эндпоинты, что и для PWA

#### Страницы
- `/` — лента объявлений (поиск, фильтр по категории, сортировка)
- `/create` — создание объявления
- `/profile` — профиль
- `/favorites` — избранное
- `/listing/:id` — детали объявления

### OK Mini App (`ok-ads/`)
- **Технологии**: Vue 3, TypeScript, Vite, OK SDK (FAPI), Pinia
- **Порт разработки**: 3002
- **Назначение**: мини-приложение Одноклассников для объявлений
- **Расположение на сервере**: `location /ok-ads/`
- **base path**: `/ok-ads/` (задан в `vite.config.ts` и `index.html`)
- **OK Application Key**: через `VITE_OK_APP_KEY` в `vite.config.ts` (по умолчанию `''` — заменить перед деплоем)
- **OK SDK**: подключён через CDN (`fapi.js`) в `index.html`, инициализация в `main.ts`

#### Страницы
- `/` — лента объявлений (поиск, фильтр, сортировка)
- `/favorites` — избранное
- `/create` — создание объявления
- `/profile` — профиль
- `/listing/:id` — детали объявления

---


## Обновления от 2026-07-04

### Подготовка проектов ok-ads и vk-ads для установки

**Тип операции**: Подготовка мини-приложений для Одноклассников и ВКонтакте

**Цель**: Подготовка проектов ok-ads и vk-ads к установке на веб-сайт vsem72 точка ру

**Описание**:
- Проект ok-ads - мини-приложение для Одноклассников, позволяющее пользователям просматривать и создавать объявления в Тюмени
- Проект vk-ads - мини-приложение для ВКонтакте, предоставляющее аналогичный функционал с интеграцией через VK Bridge
- Оба проекта используют общий бэкенд API по адресу https://vsem72.ru/api/v1
- Для полноценной работы необходимо настроить OAuth-аутентификацию через соответствующие платформы

**Необходимые доработки**:
1. Реализовать эндпоинт `POST /api/v1/auth/oauth/ok` в бэкенде для авторизации через Одноклассники
2. Реализовать эндпоинт `POST /api/v1/auth/oauth/vk` в бэкенде для авторизации через ВКонтакте
3. Настроить Nginx для обслуживания статических файлов по путям `/ok-ads/` и `/vk-ads/`
4. Получить и настроить API-ключи для соответствующих платформ

## Важные правила

1. **Docker ЗАПРЕЩЁН**
2. Не переписывай код целиком — используй diff/патчи
3. Не деплой в production без подтверждения
4. Schema-per-app — изоляция через схемы PostgreSQL
5. Thin client — Android только UI + Room-кэш
6. KSP для Hilt/Room (не kapt)
7. Kotlin 2.0+ — требуется плагин `org.jetbrains.kotlin.plugin.compose`
8. **ksp.incremental=false** для Объявления (баг KSP)

---

## Обновления от 2026-07-03

### Обновление публичной оферты
- Обновлен файл `pwa-panel/terms.html` с новым содержимым публичной оферты
- Обновленный файл успешно развернут на сервере по адресу `/var/www/html/landing/terms.html`
- В оферте учтены актуальные реквизиты: ИНН 720304206284

### Интеграция с Robokassa
- Подготовлена информация о приложениях для Robokassa
- Подтверждена доступность APK-файлов приложений:
  - Приложение "Работа": https://vsem72.ru/api/v1/releases/download/jobs/latest.apk
  - Приложение "Объявления": https://vsem72.ru/api/v1/releases/download/ads/latest.apk
- Создан файл `ROBOKASSA_APPS_INFO.md` с полной информацией для регистрации в кабинете Robokassa
- APK-файлы были созданы 2 июля 2026 года:
  - Приложение "Работа": 2 июля 2026 года, 12:53:22 UTC
  - Приложение "Объявления": 2 июля 2026 года, 12:53:25 UTC

### Доступ к VPS
- Подтвержден доступ к VPS серверу по адресу 185.221.214.239
- Проверена возможность деплоя файлов на сервер
- Подтверждена структура размещения файлов на сервере

### Структура Rust-бэкенда
- Уточнена архитектура проекта как микросервисная (не FSD):
  - API-шлюз: `backend/api-gateway/`
  - Независимые сервисы: `backend/services/` (ads, auth, payments, и т.д.)
  - Общие библиотеки: `backend/libs/` (db, cache, shared)
  - Миграции: `backend/migrations/`

### Обновление скриптов
- Обновлен скрипт `run.ps1` с возможностью автоматического деплоя оферты
- Обновлен скрипт `infra/scripts/deploy.sh` с учетом правильного пути к файлу оферты
- Добавлены новые опции в скрипты для автоматизации процессов развертывания
- Добавлены проверки зависимостей перед выполнением операций деплоя

### Проверки зависимостей
- PowerShell-скрипт теперь проверяет наличие Rust/Cargo, Node.js/npm, SSH/SCP, tar перед деплоем
- Bash-скрипт также проверяет все необходимые инструменты и файлы проекта перед началом операций
- Оба скрипта проверяют подключение к VPS перед выполнением операций

### Исправление проблемы с панелью администратора
- Исправлена конфигурация nginx для правильной работы панели администратора по адресу https://vsem72.ru/panel/
- Проблема заключалась в неправильной настройке location блока для /panel/ в конфигурации nginx
- Был изменен rewrite-блок на alias-блок для корректного обслуживания файлов PWA-панели
- Теперь панель администратора полностью функционирует и доступна по адресу https://vsem72.ru/panel/login

### Исправление проблемы с навигацией в панели управления
- Исправлен компонент заголовка [AppHeader.vue](file:///d%3A/business-platform-rust/pwa-panel/src/components/AppHeader.vue) для правильного отображения состояния аутентификации
- Добавлена проверка `v-if="authStore.isAuthenticated"` для корректного отображения кнопки "Войти" или "Выйти"
- Исправлены маршруты в [router/index.ts](file:///d%3A/business-platform-rust/pwa-panel/src/router/index.ts) для корректного перенаправления аутентифицированных пользователей
- Обновлен компонент [Landing.vue](file:///d%3A/business-platform-rust/pwa-panel/src/views/Landing.vue) для корректного отображения ссылки на панель управления в зависимости от состояния аутентификации
- Обновлены файлы PWA-панели на сервере с учетом всех исправлений

### Исправление проблемы с отображением лендинга
- Подтверждено, что лендинг на веб-сайте vsem72 точка ру продолжает работать и не исчезает при обновлениях и деплоях
- Лендинг доступен по адресу https://vsem72.ru/ и содержит всю необходимую информацию
- Правильно настроена конфигурация nginx для одновременной работы лендинга и панели управления
- Исправлена конфигурация nginx для правильной обработки статических файлов панели управления
- Статические файлы (CSS, JS) теперь корректно отдаются по адресу https://vsem72.ru/panel/assets/*

### Добавление возможности просмотра пароля на странице входа
- В форму входа [Login.vue](file:///d%3A/business-platform-rust/pwa-panel/src/views/Login.vue) добавлена возможность просмотра пароля
- Добавлен значок глаза (👁️) для показа пароля и значок (🙈) для скрытия пароля
- Реализована функциональность переключения видимости пароля при нажатии на значок
- Обновлены стили для корректного отображения значка просмотра пароля
- Изменения развернуты на сервере, теперь пользователи могут просматривать введенный пароль

### Исправление проблемы с двойным префиксом в маршрутах панели управления
- Исправлена конфигурация маршрутов в [pwa-panel/src/router/index.ts](file:///d%3A/business-platform-rust/pwa-panel/src/router/index.ts) для устранения проблемы с белым экраном при обращении к https://vsem72.ru/panel/panel
- Удален некорректный маршрут 'landing' по пути '/' из панели управления
- Установлен корректный маршрут для дашборда по пути '/' в контексте префикса '/panel/'
- Обновлены файлы панели управления на сервере
- Проверена корректная работа обоих адресов: https://vsem72.ru/panel/ и https://vsem72.ru/panel/panel

## Обновления от 2026-07-04

### Исправление проблемы с доступом к панели управления

**Тип операции**: Обновление конфигурации Nginx и файлов приложения

**Цель**: Исправление проблемы с входом на веб-сайт vsem72 точка ру в раздел администратора по адресу https://vsem72.ru/panel/login

**Описание проблемы**: 
При попытке входа на веб-сайт vsem72 точка ру в раздел администратора возникала проблема с аутентификацией. Проблема была связана с некорректной обработкой маршрутов в конфигурации Nginx для подкаталога /panel/. Использовалось правило перезаписи `rewrite ^/panel/(.*)$ /$1 break;`, которое удаляло префикс /panel/, создавая конфликт между тем, как Nginx обрабатывал маршруты, и тем, как Vue Router ожидал их получить.

**Дополнительная проблема**:
Белый экран на странице входа был вызван неправильной настройкой базового пути для статических ресурсов. Файл index.html не содержал тег <base href="/panel/">, что приводило к неправильной загрузке CSS и JS файлов.

**Влияние**:
- Исправление проблемы с входом в панель управления
- Корректная работа всех маршрутов в подкаталоге /panel/
- Обеспечение правильной функциональности Single Page Application (SPA)
- Исправление белого экрана при загрузке приложения

**Изменения в файле `infra/nginx/vsem72.ru.conf`**:
- Удалена строка `rewrite ^/panel/(.*)$ /$1 break;` из блока location /panel/
- Заменено `root /var/www/pwa;` на `alias /var/www/pwa/;` для правильного сопоставления URL с файловой системой
- Изменено `try_files $uri /index.html =404;` на `try_files $uri $uri/ /panel/index.html =404;` для обеспечения корректной обработки маршрутов Vue Router

**Изменения в файле `pwa-panel/index.html`**:
- Добавлен тег `<base href="/panel/">` для корректной загрузки статических ресурсов из подкаталога /panel/

**Инструкция по применению изменений**:
1. Скопировать обновленный файл конфигурации на сервер
2. Пересобрать приложение pwa-panel с учетом нового базового пути
3. Скопировать собранные файлы приложения на сервер
4. Выполнить проверку синтаксиса: `sudo nginx -t`
5. Перезапустить Nginx: `sudo systemctl reload nginx`

```

## Команды запуска и деплоя

### Полный деплой проекта на VPS

Для полного деплоя проекта на VPS выполните:

```
.\run.ps1 -DeployFull
```

### Обновление панели администратора на сервере

Для обновления панели администратора на сервере выполните:

```
# Обновление только панели администратора (без полного деплоя)
cd pwa-panel
npm install
npm run build
# Затем вручную скопируйте файлы из dist/ на сервер в директорию /var/www/pwa/
```

### Синхронизация JSONL-файлов с VPS

Для синхронизации JSONL-файлов с VPS выполните:

```
.\run.ps1 -SyncFeedback
```

---

## Обновления от 2026-07-05

### Подготовка vk-ads и ok-ads к деплою + бэкенд OAuth

**vk-ads (VK Mini App):**
- Добавлен `base: '/vk-ads/'` в `vite.config.ts` — пути статики корректны на продакшене
- Добавлен `<base href="/vk-ads/">` в `index.html`
- `app_id: 0` заменён на `VITE_VK_APP_ID` (env-переменная в `vite.config.ts`)
- Исправлен `scope: ''` → `scope: 'email'` в интерцепторе API
- Синхронизирован scope между `api/index.ts` и `stores/auth.ts`
- `categoryId` → `category_id` в `CreateListing.vue` (snake_case для API)
- `VITE_VK_APP_ID`, `VITE_WS_URL` добавлены в `define` конфига

**ok-ads (OK Mini App):**
- Добавлен `base: '/ok-ads/'` в `vite.config.ts` + `<base>` в `index.html`
- OK SDK подключён (`fapi.js` CDN в `index.html`, `FAPI.init()` в `main.ts`)
- Создан `tsconfig.json` (TypeScript конфиг)
- Добавлен `window.FAPI` в `env.d.ts`
- Все 5 view-компонентов переписаны — заглушки заменены на рабочий код:
  - Feed: поиск, фильтр категории, сортировка, API-запросы
  - ListingDetail: загрузка и отображение объявления
  - Favorites: список избранного
  - CreateListing: форма создания (title, description, price, category)
  - Profile: отображение пользователя, выход
- `stores/auth.ts`: реализована `loginViaOK()` через `FAPI.requireLogin()`
- `api/index.ts`: добавлены JWT-интерцептор, `fetchCategories`, `createListing`, `fetchFavorites`, `toggleFavorite`
- `App.vue`: переписан на `authStore` вместо локального `ref`
- Оранжевая тема OK (`#ed8b00`)
- Добавлены `VITE_WS_URL`, `VITE_OK_APP_KEY`, `VITE_CITY_LAT`, `VITE_CITY_LON` в конфиг

**Бэкенд (Rust):**
- Реализован `POST /api/v1/auth/oauth/vk` — принимает `{ code, vk_id, email, name }`, ищет/создаёт пользователя, возвращает JWT
- Реализован `POST /api/v1/auth/oauth/ok` — принимает `{ access_token }`, создаёт пользователя, возвращает JWT
- Добавлены структуры `OAuthVKRequest` и `OAuthOKRequest` в `models.rs`
- Маршруты зарегистрированы в `main.rs`

**Документация:**
- `AGENTS.md`: обновлены таблица эндпоинтов, секции vk/ok, добавлен раздел обновлений
- `vk-ads/README.md`, `ok-ads/README.md`: убраны `todo!()` секции, добавлены описания готовых эндпоинтов
- `docs/VK_REGISTRATION.md`, `docs/OK_REGISTRATION.md`: убраны `todo!()`, обновлены структуры проектов

---

## Обновления от 2026-07-05 (часть 2)

### Создан vk-rabota — VK Mini App «Работа»

**Тип операции**: Создание нового VK Mini App

**vk-rabota (VK Mini App Работа):**
- Создан новый проект `vk-rabota/` на базе шаблона vk-ads
- `index.html`: base `/vk-rabota/`, title «Работа — Тюмень»
- `vite.config.ts`: порт 3003, base `/vk-rabota/`, env-переменные (API, WS, город, VK App ID)
- `package.json`: зависимости Vue 3 + VK Bridge + Axios + Pinia
- `api/index.ts`: endpoints для `/jobs/vacancies`, `/jobs/categories`, `/jobs/favorites`
- `App.vue`: заголовок «Работа — Тюмень», 4 кнопки навигации (Лента, Избранное, +, Профиль)
- `views/Feed.vue`: лента вакансий (поиск, фильтр категории, сортировка зарплата/дата)
- `views/VacancyDetail.vue`: детальный просмотр вакансии (зарплата, тип, формат)
- `views/Favorites.vue`: избранное
- `views/CreateVacancy.vue`: создание вакансии (название, описание, зарплата от/до, тип занятости, формат работы, категория)
- `views/Profile.vue`: профиль + выход
- `stores/auth.ts`: VK-авторизация через VK Bridge

**Документация (маппинг Android → VK):**
- `vk-ads/README.md`: обновлён — полная таблица реализованных/нереализованных функций, сравнение Android экранов, список всех API endpoints, описание связей с Android
- `vk-rabota/README.md`: создан — полный маппинг Android-приложения Работа на VK Mini App, таблица статуса, API endpoints, структура проекта, поля вакансии

**Статус vk-ads:** ~159 kB gzip, 5 табов, WebSocket чат, feedback, подписка, auth email. Не хватает: карта, пагинация (load more), медиа в CreateListing.

**Статус vk-rabota:** ~159 kB gzip, 5 табов, WebSocket чат, feedback, подписка, auth email, отклик на вакансию. Не хватает: карта, пагинация, медиа в CreateVacancy.

**Следующие шаги:**
1. Прописать App ID `54664923` в `vk-ads/vite.config.ts` перед деплоем
2. Зарегистрировать новое приложение «Работа — Тюмень» в VK Dev, получить App ID
3. Настроить Nginx локацию `/vk-rabota/` на VPS (сейчас только `/vk-ads/`)
4. Задеплоить оба Mini App на VPS

---

## Обновления от 2026-07-05 (часть 3)

### Допилены VK Mini Apps до функционала Android

**vk-ads (Объявления):**
- **Чат WebSocket**: `stores/chat.ts` — WebSocket соединение, отправка/получение сообщений
- `views/ChatList.vue` — список диалогов (загрузка с API)
- `views/ChatRoom.vue` — комната чата (WebSocket + HTTP fallback)
- **Обратная связь**: `views/Feedback.vue` — выбор типа, тема, описание → `POST /feedback/send`
- **Подписка**: `views/Subscription.vue` — статус, оформление 200 ₽/мес → Robokassa
- **Auth email/телефон**: `views/Login.vue` + `views/Register.vue` + `views/ForgotPassword.vue`
- **Профиль**: расширен — мои объявления, ссылки на подписку и feedback
- **Навигация 5 табов**: Лента, Избранное, Чаты, Ошибки, Профиль
- **API клиент**: полный — chat, feedback, subscription, auth, media, favorites (CRUD)
- App.vue: кнопки VK-вход + email-вход, 5 кнопок нижней навигации

**vk-rabota (Работа):**
- Все те же функции, что и в vk-ads (чат, feedback, подписка, auth, профиль)
- **Отклик на вакансию**: кнопка в VacancyDetail.vue → `POST /jobs/applications`
- **Мои вакансии**: в профиле (`GET /jobs/vacancies?my=true`)
- API: `/jobs/vacancies`, `/jobs/categories`, `/jobs/favorites`, `/jobs/applications`

**Иконки:**
- `vk-ads/public/icon.png` — из `Иконки/объявления 576х576.png` (560 KB, 576×576)
- `vk-rabota/public/icon.png` — из `Иконки/работа.jpg` (318 KB, 576×576, создан скриптом)

**Документация:**
- `docs/VK_REGISTRATION.md` — обновлён: оба приложения (vk-ads App ID 54664923 + vk-rabota чек-лист)
- `vk-ads/README.md` — App ID 54664923, инструкция по смене
- `vk-rabota/README.md` — чек-лист создания приложения, ссылка на docs/VK_REGISTRATION.md

**Сборка:** оба проекта собираются успешно (~159 kB gzip, 124 модуля)

---

## Обновления от 2026-07-05 (часть 4)

### Исправление белого экрана VK Mini Apps

**Проблема:** `import bridge from '@vkontakte/vk-bridge'` на верхнем уровне JS-бандла падал вне VK (библиотека обращается к `window.parent` при инициализации). Вся сборка не монтировалась → белый экран.

**Фикс:**
- `main.ts`: статический `import bridge` заменён на динамический `import('@vkontakte/vk-bridge')` с `try/catch`
- `api/index.ts`: создана `getBridge()` — ленивая загрузка VK Bridge
- `stores/auth.ts`: то же самое — VK Bridge загружается только при вызове `loginViaVK()`

**Файлы:** `vk-ads/src/{main.ts,api/index.ts,stores/auth.ts}` и `vk-rabota/src/{main.ts,api/index.ts,stores/auth.ts}`

### Исправление роутера (base path)

**Проблема:** Vue Router без `base` не находил маршрут `/` при загрузке с `/vk-ads/` или `/vk-rabota/`. `router-view` был пуст — визуально белый экран (только шапка + меню).

**Фикс:** добавлен `base: '/vk-ads/'` и `base: '/vk-rabota/'` в `createWebHistory()`.

**Файлы:** `vk-ads/src/router/index.ts`, `vk-rabota/src/router/index.ts`

### Демо-режим

- Включён по умолчанию, если нет JWT-токена (`localStorage.demo === 'true'`)
- 5 демо-объявлений и 5 демо-вакансий с реалистичными данными (Тюмень)
- Демо-данные для: ленты, детального просмотра, избранного, чатов, профиля, подписки
- Оранжевая метка **«ДЕМО»** в шапке — клик для выхода из демо-режима
- Авто-выход из демо при логине (получении реального JWT)
- **Файл демо-данных:** `vk-ads/src/demoData.ts`, `vk-rabota/src/demoData.ts`
- **API перехватчик:** `api/index.ts` — каждая экспортированная функция проверяет `isDemo()` и возвращает мок-данные без HTTP-запроса

### Авторизация по телефону (email или телефон)

**Бэкенд:**
- `POST /api/v1/auth/login` — теперь принимает email ИЛИ телефон. Если введён `+7...` или только цифры — ищет по `phone` в БД
- `POST /api/v1/auth/register` — добавлено поле `phone` (опционально). Проверка уникальности: дубли невозможны (UNIQUE constraint + явная проверка)
- Номер очищается от форматирования (остаются только цифры) перед сохранением
- **Файлы:** `backend/api-gateway/src/routes/auth.rs` (login + register), `backend/api-gateway/src/models.rs` (RegisterRequest.phone)

**Фронтенд (оба VK Mini App):**
- Login.vue: поле «Email или телефон» — работает для обоих типов (бэкенд определяет автоматически)
- Register.vue: добавлено поле «Телефон (необязательно)» — передаётся на `POST /auth/register`
- Без SMS-подтверждения (пока)

**БД:** `auth.users.phone VARCHAR(20) UNIQUE` — уже был, используется

**Принцип:** пользователь регистрируется с email + опциональным телефоном. При логине может использовать email или телефон. Телефон уникален — повторная регистрация с тем же номером невозможна.
