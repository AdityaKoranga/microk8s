# MicroK8s Setup Guide for Ubuntu

A concise guide to install and configure MicroK8s on Ubuntu with MetalLB load balancer.

## Prerequisites

- Ubuntu 20.04+ server or virtual machine
- SSH access with sudo privileges
- **Minimum 20GB disk space** (recommended 30GB+)
- 4GB+ RAM
- Internet connectivity

## Step 1: Server Setup

Configure the server hostname and disable firewall:

```shell
sudo hostnamectl set-hostname microk8-01
sudo bash -c 'echo "$(hostname -I) microk8-01" >> /etc/hosts'
sudo systemctl stop ufw
sudo systemctl disable ufw
sudo apt update
sudo apt install -y vim curl wget jq
```



## Step 2: Install MicroK8s

Install MicroK8s using snap:

```shell
sudo apt install -y snapd jq
sudo snap install microk8s --classic
sudo snap alias microk8s.kubectl kubectl
```


## Step 3: User Configuration

Configure the current user to interact with MicroK8s:
```shell
sudo usermod -a -G microk8s $USER
newgrp microk8s
mkdir -p ~/.kube
sudo chown -f -R $USER ~/.kube
cd ~/.kube
microk8s config > config
cd ~
kubectl get all,ingress,secret,nodes --all-namespaces
```

## Step 4: Enable Core Services

Enable essential Kubernetes services:

```shell
microk8s enable dns ingress hostpath-storage helm
sudo snap alias microk8s.helm helm
kubectl get pods -A
```


## Step 5: Configure MetalLB Load Balancer

Enable MetalLB for external access to services:

```shell
microk8s enable metallb 10.100.0.1-10.100.0.20
kubectl get pods -n metallb-system
kubectl get ipaddresspool -A
```
