# Лабораторная работа №8
# Управление и аркестрация контейниризированной виртуализации в АИС

Для начала создадим контейнер
```sh
root@pcmacvirtualka:~# docker run -itd --name alpine nginx:alpine 
a3efd09ef8134ac44e8d07fe35f0a7b15189160c095011b1c36018208276a721
```
Далее останавливаем контейнер
```sh
root@pcmacvirtualka:~# docker pause alpine
alpine
```
Просмотрев контейнеры мы видим, что один остановлен
```sh
root@pcmacvirtualka:~# docker ps
CONTAINER ID   IMAGE                       COMMAND                       CREATED          STATUS                          PORTS                                       NAMES
a3efd09ef813   nginx:alpine                "/docker-entrypoint.\u2026"   11 seconds ago   Up 10 seconds (Paused)          80/tcp                                      alpine
ab3b05f11dbb   hyper/docker-registry-web   "start.sh"                    6 days ago       Restarting (1) 36 seconds ago                                               reg-web
f901ebaf025a   registry:2                  "/entrypoint.sh /etc\u2026"   6 days ago       Up 6 minutes                    0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   reg
```


```sh
```
