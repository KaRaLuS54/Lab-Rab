# Лабораторная работа №1 k8s
# Install minikub

Для начала требуется утсановить kubectl
```sh
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

Второй способ установки через прямую ссылку:
```sh
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube
```

Чтобы файл minikube был доступен для использования, используем следующие комманды:
```sh
mkdir -p /usr/local/bin/
install minikube /usr/local/bin/
```

Чтобы убедиться в том, что гипервизор и Minikube были установлены корректно, запускаем локальный кластер Kubernetes:
```sh
minikube start --vm-driver=docker
```

Rezultat
```sh
vladimir@vladimir-VirtualBox:~$ minikube start --driver=docker
😄  minikube v1.32.0 на Ubuntu 22.04
✨  Используется драйвер docker на основе конфига пользователя

💣  Exiting due to PROVIDER_DOCKER_NEWGRP: "docker version --format <no value>-<no value>:<no value>" exit status 1: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/version": dial unix /var/run/docker.sock: connect: permission denied
💡  Предложение: Add your user to the 'docker' group: 'sudo usermod -aG docker $USER && newgrp docker'
📘  Documentation: https://docs.docker.com/engine/install/linux-postinstall/
```



