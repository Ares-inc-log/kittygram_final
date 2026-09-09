# 🐱 Kittygram

## Описание

**Kittygram** — веб-приложение для любителей кошек, где пользователи могут регистрироваться, добавлять своих питомцев с фотографиями, указывать их достижения и особенности характера, а также просматривать котиков других пользователей.

Проект решает задачу создания единого пространства, где владельцы кошек могут:
- вести цифровой профиль своего питомца (фото, имя, дата рождения, достижения);
- делиться информацией о своих котиках с другими пользователями;
- просматривать ленту питомцев сообщества.

### Технологии

- **Backend**: Python, Django, Django REST Framework, Djoser (авторизация по токену)
- **Frontend**: React
- **База данных**: PostgreSQL
- **Веб-сервер**: Nginx
- **Контейнеризация**: Docker, Docker Compose
- **CI/CD**: GitHub Actions (тестирование, сборка и пуш образов в Docker Hub, автодеплой на сервер)

### Архитектура

Проект состоит из четырёх контейнеров:
- `db` — база данных PostgreSQL;
- `backend` — Django-приложение, отдающее API (Gunicorn);
- `frontend` — сборка React-приложения (копирует статику и завершает работу);
- `nginx` — веб-сервер, проксирующий запросы к API/админке и раздающий статику и медиафайлы.

## Установка

### Требования
- Установленный [Docker](https://docs.docker.com/get-docker/) и Docker Compose

### Локальный запуск через Docker Compose

1. Склонируйте репозиторий:
```bash
git clone https://github.com/Ares-inc-log/kittygram_final.git
cd kittygram_final
```

2. Создайте файл `.env` в корне проекта со следующими переменными:

POSTGRES_USER=django_user
POSTGRES_PASSWORD=ваш_пароль
POSTGRES_DB=kittygram
DB_HOST=db
DB_PORT=5432
SECRET_KEY=ваш_секретный_ключ_django



3. Запустите контейнеры:
```bash
docker compose up -d
```

4. Примените миграции и соберите статику бэкенда:
```bash
docker compose exec backend python manage.py migrate
docker compose exec backend python manage.py collectstatic
```

5. При необходимости создайте суперпользователя:
```bash
docker compose exec backend python manage.py createsuperuser
```

Проект будет доступен по адресу [http://localhost/](http://localhost/), API — по адресу [http://localhost/api/](http://localhost/api/), админ-панель — по адресу [http://localhost/admin/](http://localhost/admin/).

### Запуск backend без Docker (для разработки)

1. Перейдите в папку backend и создайте виртуальное окружение:
```bash
cd backend
python -m venv venv
source venv/bin/activate   # для Windows: venv\Scripts\activate
```

2. Установите зависимости:
```bash
pip install -r requirements.txt
```

3. Примените миграции и запустите сервер разработки:
```bash
python manage.py migrate
python manage.py runserver
```

## Примеры запросов к API

Полная документация доступна по адресу `/api/docs/` после запуска проекта.

### Регистрация пользователя

**Запрос:**
```http
POST /api/users/
Content-Type: application/json

{
  "email": "user@example.com",
  "username": "cat_lover",
  "first_name": "Иван",
  "last_name": "Иванов",
  "password": "SecurePassword123"
}
```

**Ответ:** `201 Created`
```json
{
  "email": "user@example.com",
  "id": 1,
  "username": "cat_lover",
  "first_name": "Иван",
  "last_name": "Иванов"
}
```

### Получение токена авторизации

**Запрос:**
```http
POST /api/token/login/
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123"
}
```

**Ответ:** `200 OK`
```json
{
  "auth_token": "a1b2c3d4e5f6..."
}
```

### Добавление котика

**Запрос:**
```http
POST /api/cats/
Authorization: Token a1b2c3d4e5f6...
Content-Type: application/json

{
  "name": "Мурзик",
  "birth_year": 2021,
  "color": "рыжий",
  "achievements": [
    {"name": "Лучший ловец мышей"}
  ]
}
```

**Ответ:** `201 Created`
```json
{
  "id": 5,
  "name": "Мурзик",
  "color": "рыжий",
  "birth_year": 2021,
  "owner": "cat_lover",
  "achievements": [
    {"id": 3, "name": "Лучший ловец мышей"}
  ],
  "image": null
}
```

### Получение списка котиков

**Запрос:**
```http
GET /api/cats/
Authorization: Token a1b2c3d4e5f6...
```

**Ответ:** `200 OK`
```json
[
  {
    "id": 5,
    "name": "Мурзик",
    "color": "рыжий",
    "birth_year": 2021,
    "owner": "cat_lover",
    "achievements": [],
    "image": "https://kittysen.duckdns.org/media/cats/images/murzik.jpg"
  }
]
```

### Получение данных текущего пользователя

**Запрос:**
```http
GET /api/users/me/
Authorization: Token a1b2c3d4e5f6...
```

**Ответ:** `200 OK`
```json
{
  "email": "user@example.com",
  "id": 1,
  "username": "cat_lover",
  "first_name": "Иван",
  "last_name": "Иванов"
}
```

---

## Автор

repo_owner: Ares-inc-log
[![Kittygram workflow](https://github.com/Ares-inc-log/kittygram_final/actions/workflows/kittygram_workflow.yml/badge.svg)](https://github.com/Ares-inc-log/kittygram_final/actions/workflows/kittygram_workflow.yml)

---
