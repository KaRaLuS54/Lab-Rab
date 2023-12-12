# Лабораторная работа №5
# Volume

## Создание Volume
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

Запускаем контейнер mongo с помощью команды
```sh
docker container run -d -v storage:/data/db --name mongo_test mongo
```
После повторного выполнения команды просмотра Volume, у нас появится новый
```sh
root@pcmacvirtualka:~# docker volume ls
DRIVER    VOLUME NAME
local     27b8b139eafe0e2723211e8ca60048d52e0f3b0b3d79ac913035d87c1e46d49e
'local     b2583176fe3de253d700190ec757992b0cb1d49ee18f670ae68337e5d3ca0dee'
local     c3753b1662116ebdca725b6ab3239604bcea9118bd7f9e61dcad6436962a933a
local     db
local     storage
```
Проинспектируем 
```sh
docker container inspect mongo_test
#Получим вот такой результат
"Mounts": [
            {
                "Type": "volume",
                "Name": "storage",
                "Source": "/var/lib/docker/volumes/storage/_data",
                "Destination": "/data/db",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "b2583176fe3de253d700190ec757992b0cb1d49ee18f670ae68337e5d3ca0dee",
                "Source": "/var/lib/docker/volumes/b2583176fe3de253d700190ec757992b0cb1d49ee18f670ae68337e5d3ca0dee/_data",
                "Destination": "/data/configdb",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
```

## Монтирование
С начала удаляем неиспользоемые Volume
```sh
docker volume prune -f
```

После останавливаем контейнер
```sh
docker container stop mongo_test
```

После остановки контейнера мы можем его удалить
```sh
docker container rm mongo_test
```
Теперь создаем новые Volume
```sh
docker volume create storage
docker volume create config
```

Теперь запустим контейнер прогоняя нужный порт
```sh
docker container run -d -v storage:/data/db -v config:/data/configdb -p 27018:27017 --name mongo_test mongo
```
Проинспектируем и увидим изменения
```sh
"Mounts": [
            {
                "Type": "volume",
                "Name": "config",
                "Source": "/var/lib/docker/volumes/config/_data",
                "Destination": "/data/configdb",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "storage",
                "Source": "/var/lib/docker/volumes/storage/_data",
                "Destination": "/data/db",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],
```
##Примонтируем volume к другому контейнеру
Как и в прошлые разы удаляем неиспользуемые Volume
```sh
docker volume prune -f
#Останавливаем
docker container stop mongo_test
#Удаляем
docker container rm mongo_test
```
