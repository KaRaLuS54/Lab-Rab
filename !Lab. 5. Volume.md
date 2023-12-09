# Лабораторная работа №5
# Volume

С начала удаляю неиспользуемые Volume
```sh
docker volume prune -f
```

После создаем Volume со следующими названиями
```sh
docker volume create storage
docker volume create db
```
Получим вот такой результат
```sh
root@pcmacvirtualka:~# docker volume ls
DRIVER    VOLUME NAME
local     27b8b139eafe0e2723211e8ca60048d52e0f3b0b3d79ac913035d87c1e46d49e
local     c3753b1662116ebdca725b6ab3239604bcea9118bd7f9e61dcad6436962a933a
local     db
local     storage
```
