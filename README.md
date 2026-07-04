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
| POST | `/api/v1/auth/login`    | Логин | ❌ | ✅ |
| POST | `/api/v1/auth/logout`   | Выход | ✅ | ❌ |
| POST | `/api/v1/auth/refresh`  | Обновление токена | ❌ | ✅ |
| POST | `/api/v1/auth/oauth/yandex` | OAuth Яндекс | ❌ | ✅ |
| POST | `/api/v1/auth/oauth/google` | OAuth Google | ❌ | ✅ |
| POST | `/api/v1/auth/oauth/vk` | OAuth ВКонтакте | ❌ | ✅ |
| POST | `/api/v1/auth/oauth/ok` | OAuth Одноклассники | ❌ | ✅ |

### Ads
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| GET | `/api/v1/ads/categories` | Категории объявлений | ❌ | ✅ |
| GET | `/api/v1/ads/listings`   | Объявления | ❌ | ✅ |
| POST | `/api/v1/ads/listings`  | Создать объявление | ✅ | ❌ |
| GET | `/api/v1/ads/listings/{id}` | Объявление | ❌ | ✅ |

### Jobs
| Method | Endpoint | Description | Requires Auth | Public |
|--------|----------|-------------|---------------|---------|
| GET | `/api/v1/jobs/categories` | Категории вакансий | ❌ | ✅ |
| GET | `/api/v1/jobs/listings`   | Вакансии | ❌ | ✅ |
| POST | `/api/v1/jobs/listings`  | Создать вакансию | ✅ | ❌ |
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

IP: `` (публичный IP, не Cloudflare)

### SSH-доступ
```
# Подключение
ssh 

# SSH-туннель (для локального доступа к БД)
# Прокинуть порт 
ssh 
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
ssh root@ "export HOME=/root; source $HOME/.cargo/env; cd /opt/platform/src && cargo build --release --bin api-gateway"

# 3. Заменить бинарник и перезапустить
ssh root@ "cp /opt/platform/src/target/release/api-gateway /opt/platform/api-gateway && chmod +x /opt/platform/api-gateway && systemctl restart platform"
```

### Параметры VPS
| Item | Value |
|------|-------|
| IP | `` |
| SSH | `root@` / пароль: `` |
| API | `` |

### Безопасность VPS (применено 2026-07-02)
- **Firewall**: `ufw allow 22,80,443/tcp; ufw enable`
- **SSH**: ⚠️ `PermitRootLogin yes` + пароль (пока не настроен ключ)
- **SSL**: certbot-auto для `vsem72.ru`, автопродление
- **Nginx**: reverse proxy, rate limiting, CORS, gzip
- **Backup**: ежедневно в `backup/`, удаленно (не на этом VPS)

### PostgreSQL на VPS
- Host: `127.0.0.1` / Port: `5432` (на самом VPS)
- Host: `localhost` / Port: `5433` (через SSH-туннель с Windows)
- User: `` / Pass: ``
- DB: ``

**Команды для работы (на VPS):**
```bash
sudo -u postgres psql -d baza220
pg_dump -U_user -d baza220 > backup.sql
psql -U _user -d baza220 -f backup.sql
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
scp .../Работа-debug.apk root@:/var/www/apk/jobs/latest.apk

# Объявления
scp .../app-debug.apk root@:/var/www/apk/ads/latest.apk
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
