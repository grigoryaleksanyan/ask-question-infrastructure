# AGENTS.md

## Назначение

Docker Compose-окружение для локальной разработки: PostgreSQL 16.1, MailHog и MinIO.

## Команды

- `docker compose up` — запуск инфраструктуры (detached: `docker compose up -d`)
- `docker compose down` — остановка и удаление контейнеров

## Сервисы

| Сервис | Образ | Порт | Назначение |
|--------|-------|------|-----------|
| `postgre` | `postgres:16.1-bullseye` | `5432` | PostgreSQL. Контейнер `postgre_sql`, restart `unless-stopped`. БД `AskQuestionDb` создаётся приложением (backend), инфраструктура поднимает сервер с user `postgres` / password `12345` |
| `mailhog` | `mailhog/mailhog` | `1025` (SMTP), `8025` (веб-UI) | Перехват исходящих писем. Контейнер `mailhog`, restart `unless-stopped`, fallback-порты `1025`/`8025` |
| `minio` | `minio/minio` | `9000` (S3 API), `9001` (веб-консоль) | S3-совместимое хранилище для вложений. Контейнер `minio`, restart `unless-stopped`. Bucket `ask-question-attachments` создаётся backend при старте. Credentials: `minioadmin`/`minioadmin` (из `.env`) |

## Примечания

- Docker Compose не задаёт `POSTGRES_DB`, поэтому в PostgreSQL изначально создаётся база по умолчанию (`postgres`). БД `AskQuestionDb` указывается в строке подключения backend и создаётся при первом старте приложения.
- Сервисы используют стандартную сеть Compose по умолчанию; данные PostgreSQL хранятся в bind-mount `./postgres/postgre_database` (в `.gitignore`), данные MinIO — в bind-mount `./minio/data` (в `.gitignore`).

## Конфигурация

Переменные окружения в `.env`:
- `POSTGRES_PORT=5432`
- `POSTGRES_USER=postgres`
- `POSTGRES_PASSWORD=12345`
- `MAILHOG_SMTP_PORT=1025`
- `MAILHOG_WEB_PORT=8025`
- `MINIO_API_PORT=9000`
- `MINIO_CONSOLE_PORT=9001`
- `MINIO_ROOT_USER=minioadmin`
- `MINIO_ROOT_PASSWORD=minioadmin`

## Данные

PostgreSQL данные хранятся в volume `./postgres/postgre_database:/var/lib/postgresql/data`.

MinIO данные хранятся в volume `./minio/data:/data`.

## Связь с другими сервисами

- Backend (`localhost:5500`) → PostgreSQL (`localhost:5432`)
- Backend (`localhost:5500`) → MailHog SMTP (`localhost:1025`)
- Backend (`localhost:5500`) → MinIO S3 API (`localhost:9000`)
- MailHog веб-интерфейс доступен на `http://localhost:8025`
- MinIO веб-консоль доступна на `http://localhost:9001`
