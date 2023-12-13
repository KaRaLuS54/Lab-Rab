# Лабораторная работа №6

В данной лабораторной работе решил создать Yaml для веб-приложения "Planka". Планка - предназначена для того, чтобы следить за своими задачами. Здесь можно создавать много проектов, в проектах можно создавать доски, в этих досках пишутся задачи на которые можно включить таймер, сроки выполнения, добавить ее описание и фото. В планку можно добавлять других пользователей и назначать им права и задачи, который они должны выполнить, когда сроки задачи подходят к концу - отправляется уведомления на почту пользователя.

Вот так выглядит доска с задачей, которая должна быть выполнена до 30 декабря
![Image alt](https://github.com/KaRaLuS54/picturesKaralus/111.png)

Yaml-файл выглядит таким образом:
```sh
version: '3'

services:

  nginx-proxy:
    image: nginxproxy/nginx-proxy
    ports:
      - "80:80"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
      
  planka:
    image: ghcr.io/plankanban/planka:latest
    command: >
      bash -c
        "for i in `seq 1 30`; do
          ./start.sh &&
          s=$$? && break || s=$$?;
          echo \"Tried $$i times. Waiting 5 seconds...\";
          sleep 5;
        done; (exit $$s)"
    restart: unless-stopped
    volumes:
      - user-avatars:/app/public/user-avatars
      - project-background-images:/app/public/project-background-images
      - attachments:/app/private/attachments
    ports:
      - 3000:1337
    environment:
      - BASE_URL=http://localhost
      - DATABASE_URL=postgresql://postgres@postgres/planka
      - SECRET_KEY=2600397e39236c13f0e44c59c347c8d58dc399de4ad9d91a2f6e98e190f8d4d611ba3e8668e9f40531e7ce4d620f4f2cad96e8cddd466f140f4a6d783618fc91

      - DEFAULT_ADMIN_EMAIL=demo@demo.demo # Do not remove if you want to prevent this user from being edited/deleted
      - DEFAULT_ADMIN_PASSWORD=demo
      - DEFAULT_ADMIN_NAME=Demo Demo
      - DEFAULT_ADMIN_USERNAME=demo

      - VIRTUAL_HOST=localhost
      - VIRTUAL_PORT=1337
    depends_on:
      - postgres

  postgres:
    image: postgres:14-alpine
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=planka
      - POSTGRES_HOST_AUTH_METHOD=trust

volumes:
  user-avatars:
  project-background-images:
  attachments:
  db-data:

```
