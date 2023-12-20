# Лабораторная работа №7 
# Уменьшение образа

Создадим простое приложение на Flask с подключением MongoDB.
Для этого создаем файл с именем app.py для простого Flask-приложения:
```sh
# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, Docker with Flask and MongoDB!'

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```
После создаем Dockerfile для сборки образа:

```sh
FROM python:3.8-slim

# Установка зависимостей
RUN pip install flask pymongo

# Копирование приложения
COPY app.py /app.py

# Определение переменных окружения для подключения к MongoDB
ENV MONGO_HOST=localhost
ENV MONGO_PORT=27017
ENV MONGO_DB=mydatabase

# Использование непривилегированного пользователя
USER nobody

# Запуск приложения
CMD ["python", "/app.py"]
```
Уменьшить образ можно таким способом:

```sh
# Установка базового образа
FROM python:3.9-slim-buster as build

# Установка зависимостей
RUN apt-get update && apt-get install -y gcc && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY requirements.txt /app
RUN pip install --no-cache-dir -r requirements.txt

# Копирование приложения в контейнер
COPY app.py /app

# Установка пользователя и группы
RUN groupadd -r flaskgroup && useradd -r -g flaskgroup flaskuser
USER flaskuser

# Переменные окружения для подключения к MongoDB
ENV MONGO_HOST=localhost
ENV MONGO_PORT=27017

# Запуск приложения
CMD ["python", "app.py"]

# Установка базового образа
FROM python:3.9-slim-buster

# Копирование зависимостей из предыдущего образа
COPY --from=build /usr/local/lib/python3.9 /usr/local/lib/python3.9
COPY --from=build /app /app

# Установка пользователя и группы
RUN groupadd -r flaskgroup && useradd -r -g flaskgroup flaskuser
USER flaskuser

# Переменные окружения для подключения к MongoDB
ENV MONGO_HOST=localhost
ENV MONGO_PORT=27017

# Запуск приложения
CMD ["python", "app.py"]

```
Использование переменных окружений уже есть в Dockerfile для подключения к MongoDB (MONGO_HOST, MONGO_PORT, MONGO_DB).     
        
Для сключения root из Dockerfile используется непривилегированный пользователь (USER nobody), и root исключен.

Сборка образа и публикация в репозитории делается так:
```sh
docker build -t your-username/flask-app:latest .
docker push your-username/flask-app:latest
```
Развертывание приложения локально:
```sh
docker run -p 5000:5000 -e MONGO_HOST=localhost -e MONGO_PORT=27017 -e MONGO_DB=mydatabase your-username/flask-app:latest
```
