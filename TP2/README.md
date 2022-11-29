# Docker - TP 2 Installation de Docker sous Debian


## Objectif
Installer Docker avec l'outil de paquets apt sous Debian 11 (Bullseye)

## Prérequis
Vous devez avoir une machine virtuelle (VM) sous l'os Debian 11.

Si ce n'est pas déjà le cas, dirigez-vous vers cette section => [TP1](TP1/README.md)


    Les commandes suivantes devrons être lancés directement dans sur votre VM Debian 11

## Mettre à jour son OS
```bash
sudo apt update
sudo apt upgrade -y
```

## Suivre les instructions Docker.com
Les instructions detaillés => [ici](https://docs.docker.com/engine/install/debian/)

### Les instructions directs:
```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update
```

#### Set up the repository
Update the apt package index and install packages to allow apt to use a repository over HTTPS:

```bash
 sudo apt-get update
 sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

#### Add Docker’s official GPG key:

```bash
 sudo mkdir -p /etc/apt/keyrings
 curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

#### Use the following command to set up the repository:

```bash
 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### Install Docker Engine
This procedure works for Debian on x86_64 / amd64, armhf, arm64, and Raspbian.

Update the apt package index:

```bash
 sudo apt-get update
 sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### Premier test

```bash
sudo docker run hello-world
```

<br >
<br >

* [Haut de page](README.md#menu)
* [Menu Principal](../README.md#menu-des-tp)