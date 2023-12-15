# Лабораторная работа №8
# Управление и аркестрация контейниризированной виртуализации в АИС

Для начала создадим контейнер
```sh
root@pcmacvirtualka:~# docker run -itd --name alpine nginx:alpine 
a3efd09ef8134ac44e8d07fe35f0a7b15189160c095011b1c36018208276a721
```
Далее заморозим контейнер
```sh
root@pcmacvirtualka:~# docker pause alpine
alpine
```
Просмотрев контейнеры мы видим, что один заморожен
```sh
root@pcmacvirtualka:~# docker ps
CONTAINER ID   IMAGE                       COMMAND                       CREATED          STATUS                          PORTS                                       NAMES
a3efd09ef813   nginx:alpine                "/docker-entrypoint.\u2026"   11 seconds ago   Up 10 seconds (Paused)          80/tcp                                      alpine
ab3b05f11dbb   hyper/docker-registry-web   "start.sh"                    6 days ago       Restarting (1) 36 seconds ago                                               reg-web
f901ebaf025a   registry:2                  "/entrypoint.sh /etc\u2026"   6 days ago       Up 6 minutes                    0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   reg
```

Также мы можем разморозить контейнер
```sh
root@pcmacvirtualka:~# docker unpause alpine 
alpine
```

Как видно, контейнер снова работает
```sh
root@pcmacvirtualka:~# docker ps
CONTAINER ID   IMAGE                       COMMAND                       CREATED         STATUS                          PORTS                                       NAMES
a3efd09ef813   nginx:alpine                "/docker-entrypoint.\u2026"   4 minutes ago   Up 4 minutes                    80/tcp                                      alpine
ab3b05f11dbb   hyper/docker-registry-web   "start.sh"                    6 days ago      Restarting (1) 49 seconds ago                                               reg-web
f901ebaf025a   registry:2                  "/entrypoint.sh /etc\u2026"   6 days ago      Up 10 minutes                   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   reg
```

Чтобы отобразить общесистемную информацию о состоянии и настройке Docker на хосте включая: версию ядра, количество контейнеров и образов являющимися уникальными, установленные плагины, используемый репозиторий конейнеров и т.д мы используем следующую команду
```sh
root@pcmacvirtualka:~# docker info
Client: Docker Engine - Community
 Version:    24.0.7
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.11.2
    Path:     /usr/libexec/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v2.21.0
    Path:     /usr/libexec/docker/cli-plugins/docker-compose

Server:
 Containers: 18
  Running: 2
  Paused: 0
  Stopped: 16
 Images: 9
 Server Version: 24.0.7
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Using metacopy: false
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc io.containerd.runc.v2
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: d8f198a4ed8892c764191ef7b3b06d8a2eeb5c7f
 runc version: v1.1.10-0-g18a0cb0
 init version: de40ad0
 Security Options:
  apparmor
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.2.0-39-generic
 Operating System: Ubuntu 22.04.3 LTS
 OSType: linux
 Architecture: aarch64
 CPUs: 4
 Total Memory: 3.818GiB
 Name: pcmacvirtualka
 ID: 94f2fde3-01b7-4785-8d13-8221147b0898
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false
```
Представленные сведения говорят нам в какой спецификации работает Docker на хосте. 

## Мониторинг




