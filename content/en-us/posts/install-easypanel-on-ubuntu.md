---
title: "How to Install EasyPanel on Ubuntu VPS"
description: "Step-by-step guide to install and access EasyPanel on your Ubuntu VPS to manage Docker containers through a user-friendly web interface."
categories: ["DevOps", "Infrastructure"]
tags: ["Docker", "VPS", "Linux", "Ubuntu", "EasyPanel", "Self-hosted"]
slug: "install-easypanel-on-ubuntu"
date: "2025-05-06T01:00:00-03:00"
image: "/img/install-easypanel-on-ubuntu.jpg"
---

## Install EasyPanel on Ubuntu VPS

[EasyPanel](https://easypanel.io/) is a lightweight and powerful control panel that lets you **manage Docker containers through a clean web interface**. It's ideal for developers and sysadmins who want a simpler alternative to Portainer or complex setups.

If you're hosting multiple apps or services on your server, EasyPanel helps you control containers, view logs, restart services, and manage deployments — all through your browser.

---

## ✅ Requirements

- A **clean VPS** running Ubuntu (20.04+)
- At least **4GB of RAM**
- Root access (or user with `sudo` privileges)

---

## 🔒 Secure your VPS before installing

Before installing EasyPanel, we strongly recommend you secure your VPS.  
Follow our [VPS Hardening Guide](/en-us/posts/ssh-hardening-server-linux/) to configure firewall, fail2ban, and other SSH protections.

---

## 🔥 Allow HTTP/HTTPS ports on firewall

If your VPS has UFW enabled, allow access to ports 80 and 443:

```bash
sudo ufw status
sudo ufw allow 80
sudo ufw allow 443
sudo ufw status
```

---

## 🐳 Install Docker

Run the command below to install Docker on your Ubuntu VPS:

```bash
curl -sSL https://get.docker.com | sh
```

---

## 🚀 Install EasyPanel

Run the following command to set up EasyPanel as a Docker container:

```bash
sudo docker run --rm -it   -v /etc/easypanel:/etc/easypanel   -v /var/run/docker.sock:/var/run/docker.sock:ro   easypanel/easypanel setup
```

This will download and configure EasyPanel.

---

## 📋 Verify if EasyPanel is running

Check running containers:

```bash
sudo docker ps
```

![Docker container running EasyPanel](/img/install-easypanel-on-ubuntu-1.jpg)

---

## 🌐 Access EasyPanel from the browser

Now, go to the following URL in your browser:

```
http://<YOUR_VPS_IP>:3000
```

On the first access, you'll be prompted to set your **admin email and password**.

![EasyPanel login screen](/img/install-easypanel-on-ubuntu-2.jpg)

---

## 🧭 EasyPanel Dashboard

Once logged in, you’ll see the EasyPanel dashboard where you can create apps, manage services, view logs, and more.

![EasyPanel dashboard example](/img/install-easypanel-on-ubuntu-3.jpg)

---

## ✅ Conclusion

With EasyPanel installed, you can now **manage your Ubuntu VPS via a modern web interface** using Docker containers.  
It’s a great tool to streamline development, testing, or production environments for your apps.

> Don’t forget to secure your panel access and enable HTTPS using a reverse proxy or the built-in SSL tools.
