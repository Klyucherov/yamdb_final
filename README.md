![Yamdb Workflow Status](https://github.com/klyucherov/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg?branch=master&event=push)

Проект YaMDb собирает отзывы пользователей на произведения. Произведения делятся на категории:«Книги», «Фильмы», «Музыка». Список категорий  может быть расширен (например, можно добавить категорию «Изобразительное искусство» или «Ювелирка»).
Настроика для приложения Continuous Integration и Continuous Deployment, реализация:

## Workflow
* tests - Проверка кода на соответствие стандарту PEP8 (с помощью пакета flake8) и запуск pytest. Дальнейшие шаги выполнятся только если push был в ветку master или main.
* build_and_push_to_docker_hub - Сборка и доставка докер-образов на Docker Hub
* deploy - Автоматический деплой проекта на боевой сервер. Выполняется копирование файлов из репозитория на сервер:
* send_message - Отправка уведомления в Telegram

## Cтек Разработки

[![Python](https://img.shields.io/badge/-Python-464646?style=flat&logo=Python&logoColor=56C0C0&color=008080)](https://www.python.org/)
[![Django](https://img.shields.io/badge/-Django-464646?style=flat&logo=Django&logoColor=56C0C0&color=008080)](https://www.djangoproject.com/)
[![Django REST Framework](https://img.shields.io/badge/-Django%20REST%20Framework-464646?style=flat&logo=Django%20REST%20Framework&logoColor=56C0C0&color=008080)](https://www.django-rest-framework.org/)
[![PostgreSQL](https://img.shields.io/badge/-PostgreSQL-464646?style=flat&logo=PostgreSQL&logoColor=56C0C0&color=008080)](https://www.postgresql.org/)
[![JWT](https://img.shields.io/badge/-JWT-464646?style=flat&color=008080)](https://jwt.io/)
[![Nginx](https://img.shields.io/badge/-NGINX-464646?style=flat&logo=NGINX&logoColor=56C0C0&color=008080)](https://nginx.org/ru/)
[![gunicorn](https://img.shields.io/badge/-gunicorn-464646?style=flat&logo=gunicorn&logoColor=56C0C0&color=008080)](https://gunicorn.org/)
[![Docker](https://img.shields.io/badge/-Docker-464646?style=flat&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/)
[![Docker-compose](https://img.shields.io/badge/-Docker%20compose-464646?style=flat&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/)
[![Docker Hub](https://img.shields.io/badge/-Docker%20Hub-464646?style=flat&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/products/docker-hub)
[![GitHub%20Actions](https://img.shields.io/badge/-GitHub%20Actions-464646?style=flat&logo=GitHub%20actions&logoColor=56C0C0&color=008080)](https://github.com/features/actions)
[![Yandex.Cloud](https://img.shields.io/badge/-Yandex.Cloud-464646?style=flat&logo=Yandex.Cloud&logoColor=56C0C0&color=008080)](https://cloud.yandex.ru/)


### Как запустить проект:
Все описанное ниже относится к ОС Linux.

### Клонируем репозиторий и и переходим в него:

```bash
git clone https://github.com/Klyucherov/yamdb_final.git
```
```bash
cd yamdb_final
```

### Создаем и активируем виртуальное окружение:
```bash
python3 -m venv venv
```
- Windows:
```bash
source venv/Scripts/activate
```
- Linux:
```bash
source venv/bin/activate
```
### Обновим pip:
```bash
python -m pip install --upgrade pip 
```

### Ставим зависимости из requirements.txt:
```bash
pip install -r api_yamdb/requirements.txt 
```

### Переходим в папку с файлом docker-compose.yaml:
```bash
cd infra
```

### Предварительно установим Docker на ПК под управлением Linux (Ubuntu 22.10), для Windows немного иная установка, тут не рассматриваем:
```bash
sudo apt update && apt upgrade -y
```
### Удаляем старый Docker:
```bash
sudo apt remove docker
```

### Устанавливаем Docker:
```bash
sudo apt install docker.io
```
### Смотрим версию Docker (должно выдать Docker version 20.10.16, build 20.10.16-0ubuntu1):
```bash
docker --version
```
### Активируем Docker в системе, что бы при перезагрузке запускался автоматом:
```bash
sudo systemctl enable docker
```
### Запускаем Docker:
```bash
sudo systemctl start docker
```
### Смотрим статус (выдаст статус, много букв):
```bash
sudo systemctl status docker
```
### Проверим:
```bash
sudo docker run hello-world 
```
### Не будет лишнем установить PostgreSQL:
```bash
sudo apt install postgresql postgresql-contrib -y
```

### Предварительно в папке infra создаем файл .env с следующим содержимом:
```bash
DB_ENGINE=django.db.backends.postgresql 
DB_NAME=postgres 
POSTGRES_USER=postgres 
POSTGRES_PASSWORD=postgres 
DB_HOST=db 
DB_PORT=5432
```

### Так как требование ТЗ и тестов использовать postgresql, то создадим в системе бд, установив локаль:
```bash
sudo dpkg-reconfigure locales 
```
### Выбираем ru_RU.UTF-8 нажав пробел и ждем сообщения Generation complete.
```
Generating locales (this might take a while)...
...
  ru_RU.UTF-8... done
...
Generation complete.
```
### Перезапустим систему:
```bash
sudo reboot
```
### Установка PostgreSQL:
```bash
sudo apt install postgresql postgresql-contrib -y
```
### Управляем БД:
- Остановить
```bash
sudo systemctl stop postgresql
```
```bash
- Запустить
```bash
sudo systemctl start postgresql
```
- Перезапустить
```bash
sudo systemctl restart postgresql
```
- Узнать статус, текущее состояние
```bash
sudo systemctl status postgresql
```
### Создаем бд и пользователя:
```bash
sudo -u postgres psql
```
### Создаем базу:
```sql
CREATE DATABASE test_base;
```
### Создаем пользователя:
```sql
CREATE USER test_user WITH ENCRYPTED PASSWORD 'test_pass';
```
### Даем права для пользователя:
```sql
GRANT ALL PRIVILEGES ON DATABASE test_base TO test_user;
```
### Не забываем про установку, что мы сделали ранее, активировав venv:
```bash
pip install psycopg2-binary
```
```bash
pip install python-dotenv
```
### В settings.py добавляем следующее:
```python
from dotenv import load_dotenv

load_dotenv()

...

DATABASES = {
    'default': {
        'ENGINE': os.getenv('DB_ENGINE', 'django.db.backends.postgresql'),
        'NAME': os.getenv('DB_NAME'),
        'USER': os.getenv('POSTGRES_USER'),
        'PASSWORD': os.getenv('POSTGRES_PASSWORD'),
        'HOST': os.getenv('DB_HOST'),
        'PORT': os.getenv('DB_PORT')
    }
}
```
### Далее в папке где у нас находится файл settings.py создаем файл .env:
```bash
touch .env
```
```bash
nano .env
```
```
DB_ENGINE=django.db.backends.postgresql
DB_NAME=test_base
POSTGRES_USER=test_user
POSTGRES_PASSWORD=test_pass
DB_HOST=127.0.0.1
DB_PORT=5432
```
### Не забываем про миграции (виртуальное окружение активировано):
```bash
python manage.py migrate
```

### 
Поднимаем контейнеры (
    infra_db - база,
    infra_web - веб,
    nfra_nginx - nginx сервер
    возможно пригодится команда sudo systemctl stop nginx если запускаете в DEV режиме на ПК):
```bash
sudo docker-compose up -d --build 
```

### Выполняем миграции в контейнере infra_web:
```bash
sudo docker-compose exec web python manage.py makemigrations reviews 
```
```bash
sudo docker-compose exec web python manage.py migrate --run-syncdb
```
### Создаем суперпользователя:
```bash
docker-compose exec web python manage.py createsuperuser 
```

### Собираем статику:
```bash
docker-compose exec web python manage.py collectstatic --no-input 
```

### Создаем дамп базы данных (нет в текущем репозитории):
```bash
docker-compose exec web python manage.py dumpdata > dumpPostrgeSQL.json 
```

### Останавливаем контейнеры:
```bash
docker-compose down -v 
```
### Шаблон наполнения .env (не включен в текущий репозиторий) расположенный по пути infra/.env
```
DB_ENGINE=django.db.backends.postgresql 
DB_NAME=postgres 
POSTGRES_USER=postgres 
POSTGRES_PASSWORD=postgres 
DB_HOST=db 
DB_PORT=5432 
```
### Документация API YaMDb 
Документация доступна по эндпойнту: http://130.193.54.137/redoc/

### Автор: Ключеров Евгений