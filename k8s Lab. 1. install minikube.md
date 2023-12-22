# Лабораторная работа №1 k8s
# Install minikub

Для начала требуется утсановить kubectl, однако надо, чтобы Ubuntu была с архитектурой amd64, иначе не запустится
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

Чтобы убедиться в том, что гипервизор и Minikube были установлены корректно, запускаем локальный кластер Kubernetes(перед запуском скорее всего придется установить драйвер VirtualBox, так как до его установки у меня ничего не запускалось):
```sh
minikube start --vm-driver=VirtualBox
```

Ниже видно, что vm-driver не запустился, скорее всего из-за того, что Ubuntu установлена на VirtualBox, хотя скорее всего не поддерживает архитектуру amd64
```sh
vladimir@vladimir-VirtualBox:~$ minikube start --vm-driver=VirtualBox
😄  minikube v1.32.0 на Ubuntu 22.04

❌  Exiting due to DRV_UNSUPPORTED_OS: The driver 'VirtualBox' is not supported on linux/amd64

```
Однако есть еще одна комманда запуска Миникуба
```sh
minikube start --driver=VirtualBox
```
Тут уже комманда прошла, однако в конце вышла ошибка связанная с биосом, а именно VT-X/AMD-v, видимо она выключена в VirtualBox
```sh
vladimir@vladimir-VirtualBox:~$ minikube start --driver=virtualbox
😄  minikube v1.32.0 на Ubuntu 22.04
✨  Используется драйвер virtualbox на основе конфига пользователя

🧯  The requested memory allocation of 1959MiB does not leave room for system overhead (total system memory: 1959MiB). You may face stability issues.
💡  Предложение: Start minikube with less memory allocated: 'minikube start --memory=1959mb'

💿  Downloading VM boot image ...
    > minikube-v1.32.1-amd64.iso....:  65 B / 65 B [---------] 100.00% ? p/s 0s
    > minikube-v1.32.1-amd64.iso:  292.96 MiB / 292.96 MiB  100.00% 29.91 MiB p
👍  Запускается control plane узел minikube в кластере minikube
💾  Скачивается Kubernetes v1.28.3 ...
    > preloaded-images-k8s-v18-v1...:  403.35 MiB / 403.35 MiB  100.00% 28.28 M
🔥  Creating virtualbox VM (CPUs=2, Memory=1959MB, Disk=20000MB) ...
🤦  StartHost failed, but will try again: creating host: create: precreate: This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory
🔥  Creating virtualbox VM (CPUs=2, Memory=1959MB, Disk=20000MB) ...
😿  Failed to start virtualbox VM. Running "minikube delete" may fix it: creating host: create: precreate: This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory

❌  Exiting due to HOST_VIRT_UNAVAILABLE: Failed to start host: creating host: create: precreate: This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory
💡  Предложение: Virtualization support is disabled on your computer. If you are running minikube within a VM, try '--driver=docker'. Otherwise, consult your systems BIOS manual for how to enable virtualization.
🍿  Related issues:
    ▪ https://github.com/kubernetes/minikube/issues/3900
    ▪ https://github.com/kubernetes/minikube/issues/4730
```

Статус показывает, что миникуб не был запущен
```sh
vladimir@vladimir-VirtualBox:~$ minikube status
E1221 11:06:27.128184   15661 status.go:263] The "minikube" host does not exist!
minikube
type: Control Plane
host: Nonexistent
kubelet: Nonexistent
apiserver: Nonexistent
kubeconfig: Nonexistent
```
Есть вариант попробовать запустить драйвер Docker
```sh
vladimir@vladimir-VirtualBox:~$ minikube start --driver=Docker
😄  minikube v1.32.0 на Ubuntu 22.04

💢  Exiting due to GUEST_DRIVER_MISMATCH: The existing "minikube" cluster was created using the "virtualbox" driver, which is incompatible with requested "Docker" driver.
💡  Предложение: Delete the existing 'minikube' cluster using: 'minikube delete', or start the existing 'minikube' cluster using: 'minikube start --driver=virtualbox'
```
Однако, чтобы запустить другой миникуб, надо удалить старый, который был создан, но не запушен
```sh
vladimir@vladimir-VirtualBox:~$ minikube delete
💀  Removed all traces of the "minikube" cluster.
```
После удаления пробуем ставить миникуб Docker
```sh
vladimir@vladimir-VirtualBox:~$ minikube start --driver=Docker
😄  minikube v1.32.0 на Ubuntu 22.04

❌  Exiting due to DRV_UNSUPPORTED_OS: The driver 'Docker' is not supported on linux/amd64

vladimir@vladimir-VirtualBox:~$ minikube start --vm-driver=Docker
😄  minikube v1.32.0 на Ubuntu 22.04

❌  Exiting due to DRV_UNSUPPORTED_OS: The driver 'Docker' is not supported on linux/amd64
```
Результат никакой, видимо поддержка amd64, тогда будем исправлять через биос ошибку на драйвер VirtualBox. В настройках VirtualBox недоступен данный параметр, для этого надо его запустить в биосе самой машины
![Image alt](https://github.com/KaRaLuS54/picturesKaralus/blob/main/nerabotaetVirtual.png)





