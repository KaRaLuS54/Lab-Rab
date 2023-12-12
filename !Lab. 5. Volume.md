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
## Примонтируем volume к другому контейнеру
Как и в прошлые разы удаляем неиспользуемые Volume
```sh
docker volume prune -f
```
Далее запускаем вторю mongo
```sh
docker container run -d -v storage:/data/db -v config:/data/configdb -p 27019:27017 --name mongo_test1 mongo
```
Просмотрим запущенные контейнеры
```sh
docker container ps
```
Получим вот такие данные. Контейнер не будет запущен так как Volume уже используется. При остановке уже запущенного ранее контейнера, созданный контейнер запустится.

|CONTAINER ID|IMAGE|COMMAND|CREATED|STATUS|PORTS|NAMES|
|------------|-----|-------|-------|------|-----|------|
|639510d41209|mongo |"docker-entrypoint.s\u2026"|16 minutes ago|Up 16 minutes|0.0.0.0:27018->27017/tcp,|:::27018->27017/tcp|mongo_test|
|ab3b05f11dbb|hyper/docker-registry-web|"start.sh"|3 days ago|Restarting (1) 12 seconds ago| |reg-web|
|f901ebaf025a |registry:2 |"/entrypoint.sh /etc\u2026"|3 days ago|Up 33 minutes|0.0.0.0:5000->5000/tcp,|:::5000->5000/tcp|reg|

## Установка label
Как и в прошлые разы удаляем неиспользуемые Volume
```sh
docker volume prune -f
```
Создаем Volume
```sh
docker volume create --name storage --label maintainer='Ivanov I.I.' --label used_for='mongo db storage'
```

После получим вот такой результат
```sh
root@pcmacvirtualka:~# docker volume inspect storage
[
    {
        "CreatedAt": "2023-12-09T09:28:37+03:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/storage/_data",
        "Name": "storage",
        "Options": null,
        "Scope": "local"
    }
]
```

## Манипулирование данными
Из прошлого задания можно заметить, что у нас после инспектирования storage выводится результат с строчкой `"Mountpoint": "/var/lib/docker/volumes/storage/_data"`, далее нам требуется скопировать данные во внутрь Volume. Для этого используем следующую команду
```sh
cp index.html /var/lib/docker/volumes/storage/_data

#получим вот такой результат
cp: не удалось выполнить stat для 'index.html': Нет такого файла или каталога
```

Для просмотра содержимого каталога используем следующую команду:
```sh
sudo ls -l /var/lib/docker/volumes/storage/_data

#Получим результат
Итого 276
-rw------- 1 999 docker 20480 \u0434\u0435\u043a 12 11:45 collection-0-8345376612828870226.wt
-rw------- 1 999 docker 36864 \u0434\u0435\u043a 12 11:45 collection-2-8345376612828870226.wt
-rw------- 1 999 docker  4096 \u0434\u0435\u043a  9 09:43 collection-4-8345376612828870226.wt
drwx------ 2 999 docker  4096 \u0434\u0435\u043a 12 11:45 diagnostic.data
-rw------- 1 999 docker 20480 \u0434\u0435\u043a 12 11:45 index-1-8345376612828870226.wt
-rw------- 1 999 docker 36864 \u0434\u0435\u043a 12 11:45 index-3-8345376612828870226.wt
-rw------- 1 999 docker  4096 \u0434\u0435\u043a  9 09:43 index-5-8345376612828870226.wt
-rw------- 1 999 docker  4096 \u0434\u0435\u043a 12 11:45 index-6-8345376612828870226.wt
drwx------ 2 999 docker  4096 \u0434\u0435\u043a 12 11:10 journal
-rw------- 1 999 docker 20480 \u0434\u0435\u043a 12 11:45 _mdb_catalog.wt
-rw------- 1 999 docker     0 \u0434\u0435\u043a 12 11:45 mongod.lock
-rw------- 1 999 docker 36864 \u0434\u0435\u043a 12 11:45 sizeStorer.wt
-rw------- 1 999 docker   114 \u0434\u0435\u043a  9 09:32 storage.bson
-rw------- 1 999 docker    50 \u0434\u0435\u043a  9 09:32 WiredTiger
-rw------- 1 999 docker  4096 \u0434\u0435\u043a 12 11:45 WiredTigerHS.wt
-rw------- 1 999 docker    21 \u0434\u0435\u043a  9 09:32 WiredTiger.lock
-rw------- 1 999 docker  1469 \u0434\u0435\u043a 12 11:45 WiredTiger.turtle
-rw------- 1 999 docker 69632 \u0434\u0435\u043a 12 11:45 WiredTiger.wt
```

## Создание Volume с опциями
После удаления несипользуемых Volume создаем новые
```sh
docker volume create --driver local \
    --opt type=tmpfs \
    --opt device=tmpfs \
    --opt o=size=100m \
    storage
```

После запускаем контейнер
```sh
docker container run -d -v storage:/data/db --name mongo mongo
```

## Примонтирование volume, при помощи команды --mount.
Удаляем неиспользуемые Volume, после загружем новый образ для nginx:alpine с помощью команды:
```sh
docker container run -d --mount source=storage,target=/usr/share/nginx/html --name webhost nginx:alpine
```
Получим вот такой результат
```sh
Unable to find image 'nginx:alpine' locally

alpine: Pulling from library/nginx
2c03dbb20264: Already exists 
9250f75ee527: Pull complete 
29521ceb8583: Pull complete 
c14709558ecd: Pull complete 
58a34759c9b7: Pull complete 
480fb4b12f42: Pull complete 
379d33e27177: Pull complete 
2a615eae1986: Pull complete 
Digest: sha256:3923f8de8d2214b9490e68fd6ae63ea604deddd166df2755b788bef04848b9bc
Status: Downloaded newer image for nginx:alpine
867ea42afeb35b10cbb261f245a608b1f42e18a734558bacd9f0647c17fd5650
```





