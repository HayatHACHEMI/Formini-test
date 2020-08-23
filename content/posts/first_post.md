---
title: "Création d'un cluster Kubernetes sur CentOS 7 -Test "
date: 2020-07-24T21:52:20+02:00
draft: false
author: "Hayat"
tags: ["Kubernetes", "Docker", "calico", "CentOS", "Red Hat"]
categories: ["container"]
---
# Préparation de l'environnement 
| VM | hostname | adresse IP | vCPU | vRAM |
| :--: | :--------: | :----------: | :----: | :----: |
| kubemaster |	master.formini.local |	172.15.20.10 | 2 | 2048 MB | 
| kubemaster |	master.formini.local |	172.15.20.10 | 2 | 2048 MB | 
| kubemaster |	master.formini.local |	172.15.20.10 | 2 | 2048 MB | 
| kubemaster |	master.formini.local |	172.15.20.10 | 2 | 2048 MB | 
| kubemaster |	master.formini.local |	172.15.20.10 | 2 | 2048 MB | 
| kubemaster |	master.formini.local |	172.15.20.10 | 2 | 2048 MB | 
| kubemaster |	master.formini.local |	172.15.20.10 | 2 | 2048 MB | 

```
~/.local/share/containers/storage/overlay-images
```

## Installation des prérequis 
### installation de Docker 

Nous mettons à votre disposition un script shell sur github [(install-docker.sh)](https://github.com/bkalem/kubernetes-init-join-vagrant/blob/master/install-docker.sh)


```Bash {linenos=table,linenostart=1}
 wget https://raw.githubusercontent.com/bkalem/kubernetes-init-join-vagrant/master/install-docker.sh
```

Ouvrez le compartiment ci-dessous décrivant step by step l’installation de Docker
```Bash {linenos=table,linenostart=1}
 #!/bin/bash
echo "###############################"
echo "[Task 1] Installation des prerequis Docker"
echo "###############################"
sudo yum install yum-utils device-mapper-persistent-data lvm2 -y 

echo "###############################"
echo "[Task 2] Configuration du repository Docker"
echo "###############################"
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo 

echo "###############################"
echo "[Task 3] installation Docker runtime"
echo "###############################"
sudo yum install docker-ce -y

echo "###############################"
echo "[Task 4] personalisation cgroupdriver=systemd"
echo "###############################"
sudo mkdir /etc/docker

# Set up the Docker daemon
sudo cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF

echo "###############################"
echo "[Task 5] Start & Enable Docker"
echo "###############################"
sudo systemctl start docker
sudo systemctl enable docker

echo "###############################"
echo "[Task 6] add vagrant user to docker group"
echo "###############################"
usermod -aG docker vagrant
```

```Bash {linenos=table,linenostart=1}
kubeadm join 172.15.20.10:6443 --token xxxxxx.XXXXXXXXXXX --discovery-token-ca-cert-hash sha256:XXXXXXXXXXXXXXXXXXXX
```


```Bash {linenos=table,linenostart=1}
kubectl get pods -n kube-system
```



```Bash {linenos=table,linenostart=1}
git clone https://github.com/Bahlat87/kubernetes-init-join-vagrant.git
cd kubernetes-init-join-vagrant
vagrant up
```
```Bash {linenos=table,linenostart=1}
root       3758      1  0 19:01 ?        00:00:03 /usr/bin/containerd
root       5284   3758  0 19:07 ?        00:00:00  \_ containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/f1c32e4dafd41535a36bc7975a7ed5ae797e4ff3d3f98ef97daac1541eb8
root       5299   5284  0 19:07 ?        00:00:00  |   \_ sh
root       5342   3758  0 19:07 ?        00:00:00  \_ containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/a727d0643fb27e4785cf01e6336913caa19ffa876db0da339f81cb9039e0
root       5358   5342  0 19:07 ?        00:00:00  |   \_ sh
root       5521   3758  0 19:21 ?        00:00:00  \_ containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/a23e9508b6cc39701b4c250612395ee7dbbb75d22725fdf449387621fa22
root       5537   5521  2 19:21 ?        00:00:00  |   \_ sh
root       5616   3758  0 19:21 ?        00:00:00  \_ containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/0fbd9eb1bdd450b652946997b4c38e7bb28c3d488ca5a43d4a667103e720
root       5632   5616  6 19:21 ?        00:00:00      \_ sh
root       3760      1  0 19:01 ?        00:00:01 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```