---
title: "Hardening de Servidor: Protegendo o Acesso SSH com Segurança Reforçada"
description: "Aprenda a proteger seu servidor com acesso público utilizando boas práticas de segurança no SSH, firewall, fail2ban e outras técnicas de hardening."
categories: ["Infraestrutura", "Segurança"]
tags: ["VPS", "SSH", "Linux", "Segurança", "Hardening", "Fail2ban", "Firewall"]
slug: "ssh-hardening-server-linux"
date: "2025-04-08T15:00:30-03:00"
image: "/img/ssh-hardening-server-linux.jpg"
---


## Hardening de Servidor: Protegendo o Acesso SSH com Segurança Reforçada

A internet é um ambiente hostil. Se você tem um **servidor privado com acesso via SSH**, é essencial adotar práticas de segurança para protegê-lo contra acessos indevidos.

---

## Verifique tentativas de acesso malicioso

Você pode ver as tentativas de autenticação ao seu servidor com:

```bash
sudo grep sshd /var/log/auth.log
```

Provavelmente encontrará muitas tentativas de **força bruta** feitas por scripts ou bots automatizados.

---

## Etapas para proteger seu servidor (hardening)

### ✅ 1. Crie um novo usuário sem privilégios de root

Substitua USERNAME pelo seu novo usuário

```bash
adduser USERNAME
usermod -aG sudo USERNAME
su - USERNAME
sudo whoami
```

---

### 🚫 2. Desative o login como root

Edite o arquivo `/etc/ssh/sshd_config`:

```ssh
...
PermitRootLogin no
...
```

Depois, reinicie o serviço SSH:

```bash
sudo service ssh restart
```

---

### 🔁 3. Altere a porta padrão do SSH

A porta 22 é padrão e amplamente escaneada. Escolha uma porta alta entre 49152–65535, por exemplo:

```ssh
...
Port 65222
...
```

Atualize também no `/etc/ssh/sshd_config` e reinicie o SSH.

---

### 🔒 4. Restrinja usuários permitidos

Adicione apenas o usuário criado anteriormente:

```ssh
...
AllowUsers USERNAME
...
```

---

### ⛔ 5. Limite o número de tentativas de autenticação

```ssh
...
MaxAuthTries 3
...
```

---

### 🔥 6. Configure o firewall com UFW

```bash
sudo apt update
sudo apt install ufw

sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw allow 65222
sudo ufw enable
sudo ufw status
```

---

### 🛡️ 7. Instale e configure o Fail2Ban

```bash
sudo apt install fail2ban
cd /etc/fail2ban/
sudo cp jail.conf jail.local
```

Edite o arquivo `jail.local` com a configuração abaixo:

```ini
[DEFAULT]
...

[sshd]
enabled = true
backend = systemd
port = 65222
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 6h
ignoreip = 127.0.0.1/8
```

---

### ▶️ 8. Ative o Fail2Ban

```bash
sudo systemctl restart fail2ban
sudo systemctl enable fail2ban
sudo systemctl status fail2ban
```

Tente fazer login 3 vezes com uma senha errada e depois verifique o IP banido:

```bash
sudo fail2ban-client status sshd
```

Desbanir um IP:

```bash
sudo fail2ban-client set sshd unbanip X.X.X.X
```

![Endereço IP desbanido depois do banimento](/img/ssh-hardening-server-linux-1.jpg)

---

## Conclusão

Seguindo essas práticas de **hardening no SSH**, você torna seu servidor mais seguro e menos suscetível a ataques automatizados.  
A segurança começa com a prevenção! 🔐
