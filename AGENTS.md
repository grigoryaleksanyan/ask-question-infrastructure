# AGENTS.md

## Назначение

Docker Compose-окружение для локальной разработки: PostgreSQL 16.1 и MailHog.

## Команды

- `docker compose up` — запуск инфраструктуры (detached: `docker compose up -d`)
- `docker compose down` — остановка и удаление контейнеров

## Сервисы

| Сервис | Образ | Порт | Назначение |
|--------|-------|------|-----------|
| `postgre` | `postgres:16.1-bullseye` | `5432` | PostgreSQL: БД `AskQuestionDb`, пользователь `postgres`, пароль `12345` |
| `mailhog` | `mailhog/mailhog` | `1025` (SMTP), `8025` (веб-UI) | Перехват исходящих писем для разработки |

## Конфигурация

Переменные окружения в `.env`:
- `POSTGRES_PORT=5432`
- `POSTGRES_USER=postgres`
- `POSTGRES_PASSWORD=12345`
- `MAILHOG_SMTP_PORT=1025`
- `MAILHOG_WEB_PORT=8025`

## Данные

PostgreSQL данные хранятся в volume `./postgres/postgre_database:/var/lib/postgresql/data`.

## Связь с другими сервисами

- Backend (`localhost:5500`) → PostgreSQL (`localhost:5432`)
- Backend (`localhost:5500`) → MailHog SMTP (`localhost:1025`)
- MailHog веб-интерфейс доступен на `http://localhost:8025`
