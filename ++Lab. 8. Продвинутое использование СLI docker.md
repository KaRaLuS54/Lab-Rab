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
Команда docker logs (название) отображает следующее
```sh
root@pcmacvirtualka:~# docker logs alpine
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/12/15 08:55:25 [notice] 1#1: using the "epoll" event method
2023/12/15 08:55:25 [notice] 1#1: nginx/1.25.3
2023/12/15 08:55:25 [notice] 1#1: built by gcc 12.2.1 20220924 (Alpine 12.2.1_git20220924-r10) 
2023/12/15 08:55:25 [notice] 1#1: OS: Linux 6.2.0-39-generic
2023/12/15 08:55:25 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2023/12/15 08:55:25 [notice] 1#1: start worker processes
2023/12/15 08:55:25 [notice] 1#1: start worker process 30
2023/12/15 08:55:25 [notice] 1#1: start worker process 31
2023/12/15 08:55:25 [notice] 1#1: start worker process 32
2023/12/15 08:55:25 [notice] 1#1: start worker process 33
```
Для просмотра стандартного ввода, вывода и ошибки терминала используется следующая команда
```sh
root@pcmacvirtualka:~# docker attach alpine 
2023/12/15 09:08:14 [notice] 1#1: signal 28 (SIGWINCH) received
2023/12/15 09:08:14 [notice] 33#33: signal 28 (SIGWINCH) received
2023/12/15 09:08:14 [notice] 30#30: signal 28 (SIGWINCH) received
2023/12/15 09:08:14 [notice] 32#32: signal 28 (SIGWINCH) received
2023/12/15 09:08:14 [notice] 31#31: signal 28 (SIGWINCH) received
2023/12/15 09:08:14 [notice] 30#30: signal 28 (SIGWINCH) received
2023/12/15 09:08:14 [notice] 1#1: signal 28 (SIGWINCH) received
2023/12/15 09:08:14 [notice] 33#33: signal 28 (SIGWINCH) received
2023/12/15 09:08:14 [notice] 31#31: signal 28 (SIGWINCH) received
2023/12/15 09:08:14 [notice] 32#32: signal 28 (SIGWINCH) received
```
Для мониторинга используется команда `docker stats`
```sh
CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT     MEM %     NET I/O       BLOCK I/O     PIDS
ab3b05f11dbb   reg-web   0.00%     0B / 0B               0.00%     0B / 0B       0B / 0B       0
f901ebaf025a   reg       0.00%     6.164MiB / 3.818GiB   0.16%     13.7kB / 0B   16.2MB / 0B   6
```
Чтобы выйти из мониторинга, требуется зажать комбинацию Ctr+C.
Также можно вывести только некоторые значения с помощью форматирования
```sh
docker stats --format "{{.Container}}: {{.CPUPerc}}"
```




