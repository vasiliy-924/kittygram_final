[![Main Kittygram workflow](https://github.com/vasiliy-924/kittygram_final/actions/workflows/main.yml/badge.svg)](https://github.com/vasiliy-924/kittygram_final/actions/workflows/main.yml)

## Проект Kittygram 🐈 – сервис для впечатлений от котиков

Kittygram — это веб‑приложение, где пользователи могут делиться карточками своих котиков: создавать записи с фото, цветом, достижениями и описанием, просматривать карточки других, редактировать и удалять свои, а также авторизоваться для доступа к расширенным функциям.

### Основные возможности
- **Регистрация и авторизация** (Token Auth через Djoser)
- **Создание/редактирование/удаление** карточек котиков
- **Загрузка изображений** и хранение медиа
- **Пагинация** и удобная навигация по карточкам
- **REST API** для фронтенда и внешних интеграций

## Стек
- **Backend**: Django 3.2, Django REST Framework, Djoser, PostgreSQL, Gunicorn
- **Frontend**: React (react-scripts), React Router
- **Infra/CI/CD**: Docker, Docker Compose, Nginx, GitHub Actions

## Быстрый старт (локально)
Вариант без БД PostgreSQL — с SQLite:
1. Создайте `.env` в `backend/` или в корне (см. раздел «Переменные окружения»), установите `DJANGO_USE_SQLITE=True`.
2. Установите зависимости и запустите сервер API:
   ```bash
   cd backend
   pip install -r requirements.txt
   export DJANGO_USE_SQLITE=True
   export SECRET_KEY="very_secret_kittygram_key"  # или задайте в .env
   python manage.py migrate
   python manage.py runserver
   ```
3. Запустите фронтенд:
   ```bash
   cd frontend
   npm ci
   npm start
   ```

## Продакшен (Docker Compose)
Под продакшен используется `docker-compose.production.yml` с тремя сервисами: `backend`, `frontend`, `gateway` (Nginx) и `db` (PostgreSQL).

1. Подготовьте `.env` в корне репозитория (см. ниже). Минимально нужны секции для PostgreSQL и Django.
2. Запустите:
   ```bash
   docker compose -f docker-compose.production.yml pull
   docker compose -f docker-compose.production.yml up -d
   docker compose -f docker-compose.production.yml exec backend python manage.py migrate
   docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic --noinput
   docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /static/
   ```

Состав образов (ожидаются публичными в Docker Hub):
- `kittygram_backend`
- `kittygram_frontend`
- `kittygram_gateway`

## Переменные окружения (.env)
Файл `.env` читается сервисами из `docker-compose.production.yml` и `backend/kittygram_backend/settings.py`.

Минимальный пример `.env` для продакшена:
```dotenv
# Django
SECRET_KEY=change_me
DJANGO_DEBUG=False
ALLOWED_HOSTS=example.com,www.example.com,127.0.0.1,localhost

# БД PostgreSQL
POSTGRES_DB=django_db
POSTGRES_USER=django_user
POSTGRES_PASSWORD=django_password
DB_HOST=db
DB_PORT=5432

# Локальная разработка (опционально)
DJANGO_USE_SQLITE=False
```

Секреты для CI/CD (GitHub Actions → Settings → Secrets and variables → Actions → New repository secret):
- `DOCKER_USERNAME`, `DOCKER_PASSWORD`
- `HOST` (адрес сервера), `USER` (пользователь на сервере)
- `SSH_KEY`, `SSH_PASSPHRASE`
- `TELEGRAM_TO`, `TELEGRAM_TOKEN`

## CI/CD
Workflow расположен в файле `kittygram_workflow.yml`. Он:
- запускает тесты бекенда и фронтенда;
- собирает и публикует образы в Docker Hub;
- разворачивает контейнеры на сервере с помощью `docker-compose.production.yml`;
- отправляет уведомление в Telegram.

Статус сборок отображает бейдж вверху README. Для приватных репозиториев бейдж виден только коллаборантам.

## Полезные ссылки
- Прод: `https://thunderkitty.hopto.org`
- Taski (для проверки задания): `https://thunderproject.hopto.org/`

## Автор
**Василий Петров** - [GitHub https://github.com/vasiliy-924](https://github.com/vasiliy-924)