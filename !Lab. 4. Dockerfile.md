# Лабораторная работа 4

Используя Dockerfile мы создадим новый пользовательский образ Docker на основе Ubuntu с nginx и PHP-FPM.
Для этого создаем новый каталог, в который добавим Dockerfile

```sh
mkdir -p nginx-image
cd nginx-image/
```

Создаем Dockerfile, в который будем прописывать скрипт
```sh
touch Dockerfile

#открываем файл
nano Dockerfile
```
Внутри файла начинаем прописывать скрипт, ниже будет приведен полный файл со всеми коментариями

```sh
# Скачиваем образ ubuntu 20.04
FROM ubuntu:20.04

# Добавляем подробную пользовательскую информацию об образе
LABEL maintainer="admin@admin812.ru"
LABEL version="0.1"
LABEL description="Это пользовательский образ Docker для служб
PHP-FPM и Nginx."
#в файле докера все Label прописывал на английском

# предотвратим интерактивные запросы при запуске команды apt
ARG DEBIAN_FRONTEND=noninteractive

# перед установкой любых пакетов выполняем 'apt update'
RUN apt update

# установим пакеты nginx, php-fpm и supervisord, после удаляем кэш
RUN apt install -y nginx php-fpm supervisor && \
    rm -rf /var/lib/apt/lists/* && \
    apt clean
    
# определеяем новую переменную среды, которая может быть передана в пользовательский образ
ENV nginx_vhost /etc/nginx/sites-available/default
ENV php_conf /etc/php/7.4/fpm/php.ini
ENV nginx_conf /etc/nginx/nginx.conf
ENV supervisor_conf /etc/supervisor/supervisord.conf

# Включаем PHP-fpm в nginx конфигурацию виртуального хоста
COPY default ${nginx_vhost}
RUN sed -i -e 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g' ${php_conf} && \
    echo "\ndaemon off;" >> ${nginx_conf}
    
# копируем конфигурацию supervisord
COPY supervisord.conf ${supervisor_conf}

RUN mkdir -p /run/php && \
    chown -R www-data:www-data /var/www/html && \
    chown -R www-data:www-data /run/php
    
# Volume конфигурация
VOLUME ["/etc/nginx/sites-enabled", "/etc/nginx/certs", "/etc/nginx/conf.d", "/var/log/nginx", "/var/www/html"]

# копируем start.sh скрипт и определяем настрйки контейнера по умолчанию
COPY start.sh /start.sh
CMD ["./start.sh"]

# открываем порт для приложения 
EXPOSE 80 443```

