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



