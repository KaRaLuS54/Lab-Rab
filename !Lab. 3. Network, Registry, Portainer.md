# Лабораторная работа №3
# Network, Registry, Portainer

Чтобы посмотреть какие сети настроены по умолчанию выполняем команду
```sh
docker network ls
```

|NETWORK ID   |  NAME    |  DRIVER  |  SCOPE|
|------------|---------|---------|--------|
|f104040273bd |  bridge  |  bridge  |  local|
|4fef8e669d8b |  host    |  host    |  local|
|f0bf62022d52 |  none    |  null    |  local|

Создаем новую сеть с помощью команды 
```sh
docker network create my-net
```
Повторив первую команду в таблице видны изменения, а именно добавлена новая сеть `my-net`
|NETWORK ID|     NAME|      DRIVER |   SCOPE|
|------------|---------|---------|--------|
|f104040273bd |  bridge  |  bridge  |  local|
|4fef8e669d8b |  host    |  host    |  local|
|`bd86af5b9fc1`| `my-net`  | `bridge`  |`local`|
|f0bf62022d52 |  none    |  null    |  local|

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
        "Id": "bd86af5b9fc11dea27a0b7d23d6589c4576282f0d29fc7869f0c54d03d7950a9",
        "Created": "2023-11-26T18:09:00.001762399Z",
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
Далее запускаем наш контйенер в фоновом режиме, пробрасываем 5000 порт, при выключении делаем рестартует, имя контейнера указываем reg с помощью команды
```sh
docker run -d -p 5000:5000 --restart always --name reg registry:2
```

Для просмотра выделенных ресурсов выполняем команду
docker stats

Получим вот такую табличку со значениями в реальном времени
|CONTAINER ID  | NAME      |  CPU %  |   MEM USAGE / LIMIT    | MEM %   |  NET I/O         | BLOCK I/O    | PIDS|
|------------|------------|---------|----------------------|-------|-------------|-------------|--------|
|cd1bdad0cda8  | portainer |  0.00%  |   12.4MiB / 31.42GiB   | 0.04%   |  0B / 0B         | 0B / 528kB   | 10|
|6c6ca67ffea1  | reg-web   |  0.62%  |   1.198GiB / 31.42GiB  | 3.81%   |  42B / 0B        | 0B / 0B      | 31|
|590d573191ff  | reg       |  0.00%  |   13.74MiB / 31.42GiB  | 0.04%   |  70.8MB / 218kB  | 0B / 107MB   | 12|
|168d3ea06d39  | my-nginx  |  0.00%  |   10.77MiB / 31.42GiB  | 0.03%   |  42B / 0B        | 0B / 12.3kB  | 9|
