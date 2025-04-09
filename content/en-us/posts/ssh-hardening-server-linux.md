---
title: "Server Hardening: Securing SSH Access with Enhanced Protection"
description: "Learn how to protect your public-facing server by applying security best practices for SSH, firewall, fail2ban, and other hardening techniques."
categories: ["Infrastructure", "Security"]
tags: ["VPS", "SSH", "Linux", "Security", "Hardening", "Fail2ban", "Firewall"]
slug: "ssh-hardening-server-linux"
date: "2025-04-08T15:00:30-03:00"
image: "/img/ssh-hardening-server-linux.jpg"
---


## Server Hardening: Securing SSH Access with Enhanced Protection

The internet is a hostile environment. If you have a **private server with SSH access**, it's essential to apply security measures to prevent unauthorized access.

---

## Check for Malicious Access Attempts

You can see authentication attempts to your server using:

```bash
sudo grep sshd /var/log/auth.log
```

You’ll likely find several **brute-force** attempts from automated bots or scripts.

---

## Steps to Secure Your Server (Hardening)

### ✅ 1. Create a New Non-Root User

Replace USERNAME with your preferred username:

```bash
adduser USERNAME
usermod -aG sudo USERNAME
su - USERNAME
sudo whoami
```

---

### 🚫 2. Disable Root Login

Edit the `/etc/ssh/sshd_config` file:

```ssh
...
PermitRootLogin no
...
```

Then restart the SSH service:

```bash
sudo service ssh restart
```

---

### 🔁 3. Change the Default SSH Port

Port 22 is the default and often scanned. Choose a high port between 49152–65535, for example:

```ssh
...
Port 65222
...
```

Update the `/etc/ssh/sshd_config` and restart SSH.

---

### 🔒 4. Restrict Allowed Users

Only allow the new user you created:

```ssh
...
AllowUsers USERNAME
...
```

---

### ⛔ 5. Limit Authentication Attempts

```ssh
...
MaxAuthTries 3
...
```

---

### 🔥 6. Configure Firewall with UFW

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

### 🛡️ 7. Install and Configure Fail2Ban

```bash
sudo apt install fail2ban
cd /etc/fail2ban/
sudo cp jail.conf jail.local
```

Edit `jail.local` with the following configuration:

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

### ▶️ 8. Enable Fail2Ban

```bash
sudo systemctl restart fail2ban
sudo systemctl enable fail2ban
sudo systemctl status fail2ban
```

Attempt to log in 3 times with a wrong password, then check banned IPs:

```bash
sudo fail2ban-client status sshd
```

To unban an IP:

```bash
sudo fail2ban-client set sshd unbanip X.X.X.X
```

![Unbanned IP address after being blocked](/img/ssh-hardening-server-linux-1.jpg)

---

## Conclusion

By following these **SSH hardening** practices, your server becomes more secure and less vulnerable to automated attacks.  
Security starts with prevention! 🔐
