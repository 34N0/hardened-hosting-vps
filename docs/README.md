# 🚧 Ubuntu Server Dokku

This repository scripts used for an initial set up a hardened ubuntu 22.04 LTS server using the dokku service to host websites in a production environment.

## 📃 Introduction

Setting up a secure web hosting environment involves configuring various components such as web servers, reverse proxies, and scripts for automation. This repository provides a standardized structure for managing these components using Docker containers and scripts. 

## 🏗️ Setup

### 👩🏻‍🍳 Create "host" user

To create a new user called "host," use the adduser command:

```bash
adduser host
```
add to sudo group:
```bash
usermod -aG sudo host
```
copy root ssh keys:
```bash
rsync --archive --chown=host:host /root/.ssh /home/host
```
Login as the newly created "host" user now.

### 🍳 Run setup script

"Clone" this repo into the "host" user home:
```bash
git init
git remote add origin https://github.com/34N0/ubuntu-server-dokku
git fetch
git checkout origin/main -ft
```

run the setup script:
```bash
sudo bash setup.sh
```

## 🐋 Install Dokku

download the installation script:
```bash
wget -NP . https://dokku.com/bootstrap.sh
```
run the installer:
```bash
sudo DOKKU_TAG=v0.32.0 bash bootstrap.sh
```
configure your server domain
```bash
dokku domains:set-global dokku.me
```
and your ssh key to the dokku user:
```bash
PUBLIC_KEY="your-public-key-contents-here"
echo "$PUBLIC_KEY" | dokku ssh-keys:add admin
```

For automatic SSL use [dokku-letsencrypt](https://github.com/dokku/dokku-letsencrypt)

## 🚧 Configuration

### 📱 SSH

#### Harden

1. Open the /etc/ssh/sshd_config 

2. Set the following:
custom port:
```bash
Port <port_number>
```
restrict the maximum aut tries to 4:
```bash
MaxAuthTries 4
```
disalbe host based auth:
```bash
IgnoreRhosts yes
```
disalbe root login:
```bash
PermitRootLogin no
```
(assuming you're using key auth) disable passwort auth:
```bash
PasswordAuthentication no
```
disable challenge response:
```bash
ChallengeResponseAuthentication no
```
set max idle connections to 5min:
```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```
3. restart sshd
```bash
systemctl restart sshd
```

#### 2FA

Follow the official [Ubuntu Guide](https://ubuntu.com/tutorials/configure-ssh-2fa#2-installing-and-configuring-required-packages).

### ⛑️ CIS Security Compliance

Make sure you read the [CIS Benchmark](CIS_Ubuntu_Linux_22.04_LTS_Benchmark_v1.0.0.pdf) first.

To achieve CIS Level 2 Compliance attach the machine to [Ubuntu Pro](https://ubuntu.com/pro/tutorial) and follow the instructions on [CIS setup](https://ubuntu.com/security/certifications/docs/usg/cis).

### 🧱 UFW

Set default policies:
```bash
sudo ufw default deny incoming
sudo ufw default deny outgoing
```
Allow Connections:
```bash
sudo ufw allow <ssh port>
sudo ufw allow http
sudo ufw allow https
```

## 🤝 Contribute

We welcome contributions from the community to improve and enhance this project.