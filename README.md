# praktikum_new_diplom
![foodgram-project-react Workflow Status](https://github.com/leonidvanyukov/foodgram-project-react/actions/workflows/workflow.yml/badge.svg?branch=master&event=push)
## Запуск в Docker
```bash
# В секретах Actions на GitHub создаем необходимые переменные с вашими данными
ALLOWED_HOSTS # Список разрешенных хостов, указывается адрес вашего сервера
DB_ENGINE # Какая база будет использоваться
DB_HOST # Адрес БД
DB_PORT # Порт БД
HOST # Адрес вашего сервера
MY_LOGIN # Логин для Docker Hub
MY_PASS # Пароль для Docker Hub
PASSPHRASE # Пароль для сертификата
POSTGRES_DB # Имя БД
POSTGRES_PASSWORD # Пароль БД
POSTGRES_USER # Пользователь БД 
SECRET_KEY # Секретный ключ для django
SSH_KEY # SSH ключ для доступа к серверу
USER # Имя пользователя на ВМ
```

1. Предварительно необходимо установить на ВМ в облаке Docker.
2. Создаем папку /infra в домашней директории вашего пользователя ВМ:
```bash
cd ~
mkdir infra
```

3. Из папки вашего проекта на локальном компьютере необходимо создать и загрузить образы в Docker Hub:

```bash
docker login -u вашлогин
# Создаем и загружаем бэк
cd backend
docker build -t вашлогин/foodgram:latest .
docker push вашлогин/foodgram:latest
cd ..
# Создаем и загружаем фронт
cd frontend
docker build -t вашлогин/foodgram_frontend:latest .
docker push вашлогин/foodgram_frontend:latest
```

4. Необходимо перенести файлы docker-compose.yml и default.conf на ВМ из папки infra:

```bash
cd infra
scp docker-compose.yml username@server_ip:/home/вашлогин/
scp default.conf username@server_ip:/home/вашлогин/
```

5. Создать файл .env в папке infra на ВМ:

```bash
touch .env
```

6. Добавить в файл данные

```python
DB_ENGINE='django.db.backends.postgresql'
POSTGRES_DB='Имя БД'
POSTGRES_USER='пользователь БД'
POSTGRES_PASSWORD='Пароль БД'
DB_HOST='Адрес БД'
DB_PORT='Порт БД'
SECRET_KEY='Секретный ключ django'
ALLOWED_HOSTS='Адрес ВМ'
DEBUG = False
```

7. Запускаем образ

```bash
sudo docker-compose up -d --build
```

8. Выполняем обязательные команды:

```bash
sudo docker-compose exec backend python manage.py makemigrations
sudo docker-compose exec backend python manage.py migrate --noinput
sudo docker-compose exec backend python manage.py createsuperuser
sudo docker-compose exec backend python manage.py collectstatic --no-input
sudo docker-compose exec backend python manage.py load_tags
sudo docker-compose exec backend python manage.py load_ingrs
```
### Документация к API доступна после запуска

```url
http://127.0.0.1/api/docs/
```
```
Адрес сервера - http://84.201.129.17/
Логин - leonid@vanyukov.ru
Пароль - testpassword
```