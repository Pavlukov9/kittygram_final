# Kittygram

Проект представляет собой социальную сеть для обмена фотографиями любимых питомцев.

## Описание проекта

Пользователи могут регистрироваться, загружать фотографии своих питомцев, описание их достижений, а также смотреть питомцев других пользователей.

## Технологии

- Python 3.11.1
- node.js 
- React (frontend) 
- Django 3.2.3 (backend) 
- Gunicorn 20.1.0
- Nginx
- djangorestframework 3.12.4
- PostgreSQL 13.10

## Установка (указана для Linux)

    1. Склонируйте репозиторий на свой компьютер:
    ```git clone https://github.com/Pavlukov9/kittygram_final.git```

    2. Создайте файл `.env` и заполните его своими данными. Все необходимые переменные указаны в файле `.env.example` в корневой директории проекта.

## Создание Docker-образов

    1. Создание образов. Замените `<username>` на свой логин на DockerHub:

    ```
    cd backend
    docker build -t <username>/kittygram_backend .
    cd -
    cd frontend
    docker build -t <username>/kittygram_frontend .
    cd -
    cd nginx
    docker build -t <username>/kittygram_nginx .
    cd -
    ```

    2. Загрузите образы на свой аккаунт на DockerHub:

    ```
    docker push <username>/kittygram_backend
    docker push <username>/kittygram_frontend
    docker push <username>/kittygram_nginx
    ```

## Деплой на сервере

    1. Подключитесь к удалённому серверу (показан пример кода в GitBash):
    
    ```
    ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом_без_расширения login@ip 
    ```

    2. Создайте на сервере директорию `kittygram`

    ```mkdir kittygram```

    3. Установите Docker Compose на удалённый сервер:

    ```
    sudo apt update
    sudo apt install curl
    sudo -fSL https://get.docker.com -o get-docker.sh
    sudo sh ./get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```

    4. Создайте на сервере в директории `/kittygram` файлы `docker-compose.production.yml` и `.env` и скопируйте в них код из проекта при помощи редактора `nano` (чтобы сохранить код в редакторе `nano`: `Ctrl+S`, чтобы выйти из этого редактора: `Ctrl+X`):

    ```
    touch docker-compose.production.yml
    touch .env
    sudo nano docker-compose.production.yml
    sudo nano .env
    ``` 

    5. Запустите Docker Compose в режиме демона и проверьте, что все контейнеры запустились:

    ```
    sudo docker compose -f docker-compose.production.yml up -d
    sudo docker compose -f docker-compose.production.yml ps
    ```

    6. Выполните миграции, соберите статические файлы и скопируйте их в `/backend_static/static/`:

    ```
    sudo docker-compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker-compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker-compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
    ```

    7. Откройте файл конфигурации Nginx в редакторе nano и измените настройки `location` в секции `server`:

    ```
    sudo nano /etc/nginx/sites-enabled/default
    ```
    ```
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9080;
    }
    ```

    8. Проверьте правильность конфигурации Nginx:

    ```sudo nginx -t```

    Eсли ответ следующий, то ошибок нет:

    ```
    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful
    ```

    9. Перезапустите Nginx:

    ```sudo service nginx reload```

## Настройка CI/CD

    1. Главный файл workflow уже полностью написан и находится в директории:

    ```kittygram/.github/workflows/main.yml```

    2. Чтобы его адаптировать к вашему удалённому серверу, нужно добавить секреты в GitHub Actions:

    ```
    DOCKER_USERNAME       # имя пользователя на DockerHub
    DOCKER_PASSWORD       # пароль пользователя на DockerHub
    HOST                  # IP-адрес сервера
    USER                  # имя пользователя
    SSH_KEY               # приватный SSH-ключ
    SSH_PASSPHRASE        # пароль для SSH-ключа

    Если вы хотите получать уведомление в Telegram об успешном выполнении деплоя на сервер, то добавьте ещё:

    TELEGRAM_TO           # ID вашего телеграм-аккаунта (узнать можно у @userinfobot)
    TELEGRAM_TOKEN        # токен вашего телеграм-бота (узнать можно у @BotFather)
    ```

## Автор

[Павлюков Даниил](https://github.com/Pavlukov9)
