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
EXPOSE 80 443
```

Создаем новую конфигурацию виртуального хоста nginx
```sh
nano default
```

Закинем в файл такие настройки:
```sh
server {
    listen 80 default_server;
 
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;
 
    server_name _;
 
    location / {
        try_files $uri $uri/ =404;
    }
 
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;
    }
}

```

Теперь можем создать конфигурацию ‘supervisrod.conf’, которая будет содержать некоторые параметры  Nginx и PHP-FPM
```sh
nano supervisord.conf
```
В файл закидываем следующее
```sh
[unix_http_server]
file=/dev/shm/supervisor.sock   ; (the path to the socket file)
 
[supervisord]
logfile=/var/log/supervisord.log ; (main log file;default $CWD/supervisord.log)
logfile_maxbytes=50MB        ; (max main logfile bytes b4 rotation;default 50MB)
logfile_backups=10           ; (num of main logfile rotation backups;default 10)
loglevel=info                ; (log level;default info; others: debug,warn,trace)
pidfile=/tmp/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
nodaemon=false               ; (start in foreground if true;default false)
minfds=1024                  ; (min. avail startup file descriptors;default 1024)
minprocs=200                 ; (min. avail process descriptors;default 200)
user=root             ;

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
 
[supervisorctl]
serverurl=unix:///dev/shm/supervisor.sock ; use a unix:// URL  for a unix socket
 
[include]
files = /etc/supervisor/conf.d/*.conf
 
[program:php-fpm7.4]
command=/usr/sbin/php-fpm7.4 -F
numprocs=1
autostart=true
autorestart=true
 
[program:nginx]
command=/usr/sbin/nginx
numprocs=1
autostart=true
autorestart=true
```
Теперь можем создать последний скрипт ‘start.sh’, он будет содержать команду supervisord для запуска
```sh
nano start.sh
```

Скрипт выглядит следующим образом
```sh
#!/bin/sh
/usr/bin/supervisord -n -c /etc/supervisor/supervisord.conf
```

Теперь надо выдать права нашему скрипту с помощью команды

```sh
chmod +x start.sh
```

Прописав команду 'tree .' мы можем увидеть конфигурацию нашего пользовательского образа
```sh
root@pcmacvirtualka:~/nginx-image# tree .
|
| ________default
| ________Dockerfile
| ________start.sh
| ________supervisord.conf

0 directories, 4 files
root@pcmacvirtualka:~/nginx-image#

```











