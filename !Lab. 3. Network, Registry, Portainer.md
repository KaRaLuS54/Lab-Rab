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

Далее проводим инспектирование нашей сети my-net
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


