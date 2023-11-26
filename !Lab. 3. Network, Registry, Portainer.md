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
