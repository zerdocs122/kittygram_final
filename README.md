# KittyGram Final

Финальный проект спринта "Контейнеры и CI/CD". Социальная сеть для обмена фотографиями котиков с полным CI/CD пайплайном.

## Описание проекта

KittyGram — это веб-приложение, позволяющее пользователям делиться фотографиями своих котиков.
Проект реализован с использованием микросервисной архитектуры в Docker-контейнерах и включает автоматизированный пайплайн развертывания через GitHub Actions.

## CI/CD пайплайн
При пуше в ветку main автоматически выполняются:

1. Тестирование
- Проверка кода на соответствие PEP8 (flake8)

- Запуск тестов Django (backend)

- Запуск тестов React (frontend)

2. Сборка образов

- Сборка Docker-образов для всех компонентов

- Пуш образов на Docker Hub:

kidik4/kittygram_backend:latest

kidik4/kittygram_frontend:latest

kidik4/kittygram_gateway:latest

3. Деплой на сервер

- Копирование docker-compose.production.yml на сервер

- Остановка старых контейнеров

- Запуск новых контейнеров из обновленных образов

- Применение миграций базы данных

- Сбор статических файлов

4. Уведомления

- Отправка сообщения в Telegram об успешном деплое

## Технологический стек

### Backend
- **Python 3.9** — основной язык программирования
- **Django 3.2.3** — веб-фреймворк
- **Django REST Framework 3.12.4** — REST API
- **Djoser 2.1.0** — система аутентификации
- **PostgreSQL 13** — база данных
- **Gunicorn 23.0.0** — WSGI-сервер

### Frontend
- **React** — интерфейс пользователя

### Инфраструктура
- **Docker & Docker Compose** — контейнеризация и оркестрация
- **Nginx 1.22.1** — веб-сервер и обратный прокси
- **GitHub Actions** — CI/CD пайплайн
- **Docker Hub** — реестр Docker-образов

## Быстрый старт

### Предварительные требования
- Docker и Docker Compose
- Git
- Python 3.9+ (для локальной разработки)

### Локальный запуск (Development)

1. Клонируйте репозиторий:
```bash
git clone https://github.com/kidik4/kittygram_final.git
cd kittygram_final

2. Создайте файл окружения:

cp .env.example .env

3. Настройте переменные окружения в .env:
POSTGRES_DB=django
POSTGRES_USER=django
POSTGRES_PASSWORD=ваш_надёжный_пароль
DB_HOST=db
DB_PORT=5432
SECRET_KEY=секретный ключ из settings.py

4. Соберите и запустите контейнеры:
docker compose up --build

5. Примените миграции базы данных:
docker compose exec backend python manage.py migrate

6. Соберите статические файлы Django:
docker compose exec backend python manage.py collectstatic

7. Создайте суперпользователя (опционально):
docker compose exec backend python manage.py createsuperuser

8 Откройте приложение в браузере:
http://localhost:9000

## Продакшен-развертывание (Production)

Используйте production-композ файл:

docker compose -f docker-compose.production.yml up -d

## API Reference

1. Аутентификация

POST /api/token/login/
Content-Type: application/json

KittyGram использует Token-аутентификацию. Получите токен через `/api/token/login/` и передавайте его в заголовке:

{
    "username": "username",
    "password": "password"
}

-Ответ:
	{
    "auth_token": "ваш_токен_авторизации"
}

2. Получение списка всех котиков

GET /api/cats/
Authorization: Token ваш_токен

- Ответ:
	[
    {
        "id": 1,
        "name": "Мурзик",
        "color": "Рыжий",
        "birth_year": 2020,
        "age": 4,
        "image": "http://ваш-домен/media/cats/murzik.jpg",
        "image_url": "http://ваш-домен/media/cats/murzik.jpg",
        "owner": 1,
        "achievements": [
            {"id": 1, "achievement_name": "Ловец мышей"},
            {"id": 2, "achievement_name": "Ласковый"}
        ]
    }
]

3. Создание нового котика

POST /api/cats/
Authorization: Token ваш_токен
Content-Type: application/json

- Тело запроса:

{
    "name": "Барсик",
    "color": "#FF5733",  // HEX-код цвета или название
    "birth_year": 2019,
    "image": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...", // Base64 изображение (опционально)
    "achievements": [
        {"achievement_name": "Умный"},
        {"achievement_name": "Игривый"}
    ]
}

## Автор

GitHub: zerdocs
Docker Hub: kidik4
