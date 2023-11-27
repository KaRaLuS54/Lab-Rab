# Лабораторная работа №3
# Network, Registry, Portainer

Чтобы посмотреть какие сети настроены по умолчанию выполняем команду
```sh
docker network ls
```

|NETWORK ID   |  NAME    |  DRIVER  |  SCOPE|
|------------|---------|---------|--------|
|537e49c938fc  |  bridge  |  bridge  |  local|
|b9897869bf4b |  host    |  host    |  local|
|9ad6b3820e9f |  none    |  null    |  local|

Создаем новую сеть с помощью команды 
```sh
docker network create my-net
```
Повторив первую команду в таблице видны изменения, а именно добавлена новая сеть `my-net`
|NETWORK ID|     NAME|      DRIVER |   SCOPE|
|------------|---------|---------|--------|
|537e49c938fc|  bridge  |  bridge  |  local|
|b9897869bf4b|  host    |  host    |  local|
|`513d6b8f897f`| `my-net`  | `bridge`  |`local`|
|9ad6b3820e9f |  none    |  null    |  local|

Далее создаем nginx
```sh
docker run --name my-nginx \
  -d --publish 81:80 \
  nginx
```

После подкючения к сети наш nginx командой `docker network connect my-net my-nginx` проводим инспектирование нашей сети my-net
```sh
docker network inspect my-net
#получаем вот такой результат
[
    {
        "Name": "my-net",
        "Id": "513d6b8f897f9865dd1d20d9b1c3318113975513f4a23ba554103df65af33230",
        "Created": "2023-11-27T09:38:28.584505052Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "3268ee25e09f314ba2d47cba85274270d14ae6b4d58f167c76c3f5d183479dcd": {
                "Name": "my-nginx",
                "EndpointID": "84ee80508bf0e6e215fa10c32f0b8298eaa5697956073608a200b1ea06dd4124",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

Если отключим контейнер с помощью команды
```sh
docker network disconnect my-net my-nginx
#то получим вот такой результат:
[
    {
        "Name": "my-net",
        "Id": "513d6b8f897f9865dd1d20d9b1c3318113975513f4a23ba554103df65af33230",
        "Created": "2023-11-27T09:38:28.584505052Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```
После в задании лабораторной лаботы было показано как присоединить контейнер к host
```sh
docker network create -d host my-net-host
```
Но так как у нас уже есть контейнер с присоединенным хостом, то нам выводит ошибку `Error response from daemon: only one instance of "host" network is allowed`


Запустим наш контйенер в фоновом режиме, пробрасываем 5000 порт, при выключении делаем рестартует, имя контейнера указываем reg с помощью команды
```sh
docker run -d -p 5000:5000 --restart always --name reg registry:2
```
Подключаем UI интерфейс
```sh
docker run -itd -p 8081:8080 --restart always --name reg-web --link reg -e REGISTRY_URL=http://reg:5000/v2 -e REGISTRY_NAME=localhost:5000 hyper/docker-registry-web
```
Создаем тег для источника nginx
```sh
docker image tag nginx localhost:5000/nginx:v1
```
После добавляем в образ тег в локальный registry
```sh
docker push localhost:5000/nginx:v1
```


Для просмотра выделенных ресурсов выполняем команду
```sh
docker stats
```

Получим вот такую табличку со значениями в реальном времени
|CONTAINER ID  | NAME      |  CPU %  |   MEM USAGE / LIMIT    | MEM %   |  NET I/O         | BLOCK I/O    | PIDS|
|------------|------------|---------|----------------------|-------|-------------|-------------|--------|
|cd1bdad0cda8  | portainer |  0.00%  |   12.4MiB / 31.42GiB   | 0.04%   |  0B / 0B         | 0B / 528kB   | 10|
|6c6ca67ffea1  | reg-web   |  0.62%  |   1.198GiB / 31.42GiB  | 3.81%   |  42B / 0B        | 0B / 0B      | 31|
|590d573191ff  | reg       |  0.00%  |   13.74MiB / 31.42GiB  | 0.04%   |  70.8MB / 218kB  | 0B / 107MB   | 12|
|168d3ea06d39  | my-nginx  |  0.00%  |   10.77MiB / 31.42GiB  | 0.03%   |  42B / 0B        | 0B / 12.3kB  | 9|

##Ограничения
Для ограничения по памяти Mb использовали команду
```sh
docker run -d -p 8083:80 -m 50m nginx
```

Далее ограничивем использование CPU чтобы было 1
```sh
docker run -d -p 8083:80 -m 50m --cpus 1 --name ng_limit nginx
```
Так мы используем одну часть CPU c минимальной единицой в 0,01
```sh
docker run -d -p 8083:80 -m 50m --cpus 0.01 --name ng_limit nginx
```






