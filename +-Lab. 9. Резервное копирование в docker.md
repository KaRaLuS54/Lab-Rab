# Лабораторная работа №9
# Резервное копирование в docker
Для создания резервной копии данных в контейнере используем официальный образ PostgreSQL, чтобы продемонстрировать резервное копирование базы данных. С начала надо запустить контейнер PostgreSQL
```sh
docker run --name mypostgres -e POSTGRES_PASSWORD=mypassword -d postgres
```
Далее делаем резервную копию базы данных
```sh
docker exec -i mypostgres pg_dump -U postgres mydatabase > backup.sql
```
После проверяем созданную БД
```sh
cat backup.sql
```
Теперь останавливаем и удаляем контейнер
```sh
docker stop mypostgres
docker rm mypostgres
```
Теперь требуется восстановить БД, для этого выполняем следующие комманды
```sh
docker run --name mypostgres-restored -e POSTGRES_PASSWORD=mypassword -d postgres
docker exec -i mypostgres-restored psql -U postgres mydatabase < backup.sql
```

Эти команды создают новый контейнер mypostgres-restored и восстанавливают базу данных из резервной копии backup.sql
