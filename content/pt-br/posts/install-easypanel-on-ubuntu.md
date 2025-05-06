---
title: "Como Instalar o EasyPanel em um VPS Ubuntu"
description: "Guia passo a passo para instalar e acessar o EasyPanel no seu VPS Ubuntu e gerenciar containers Docker via uma interface web amigável."
categories: ["DevOps", "Infraestrutura"]
tags: ["Docker", "VPS", "Linux", "Ubuntu", "EasyPanel", "Self-hosted"]
slug: "como-instalar-easypanel-no-ubuntu"
date: "2025-05-06T01:00:00-03:00"
image: "/img/install-easypanel-on-ubuntu.jpg"
---

## Instale o EasyPanel em um VPS Ubuntu

[EasyPanel](https://easypanel.io/) é um painel de controle leve e poderoso que permite **gerenciar containers Docker por meio de uma interface web limpa**. Ideal para desenvolvedores e sysadmins que querem uma alternativa mais simples ao Portainer ou configurações complexas.

Se você hospeda múltiplos apps ou serviços no seu servidor, o EasyPanel ajuda a controlar containers, visualizar logs, reiniciar serviços e gerenciar deploys — tudo pelo navegador.

---

## ✅ Requisitos

- Um **VPS limpo** rodando Ubuntu (20.04+)
- Pelo menos **4GB de RAM**
- Acesso root (ou usuário com privilégios `sudo`)

---

## 🔒 Proteja seu VPS antes da instalação

Antes de instalar o EasyPanel, recomendamos fortemente proteger seu servidor.  
Siga nosso [guia de hardening de VPS](/pt-br/posts/ssh-hardening-servidor-linux/) para configurar firewall, fail2ban e proteções no SSH.

---

## 🔥 Libere as portas HTTP/HTTPS no firewall

Se estiver usando UFW, libere as portas 80 e 443:

```bash
sudo ufw status
sudo ufw allow 80
sudo ufw allow 443
sudo ufw status
```

---

## 🐳 Instale o Docker

Execute o comando abaixo para instalar o Docker no Ubuntu:

```bash
curl -sSL https://get.docker.com | sh
```

---

## 🚀 Instale o EasyPanel

Execute o comando abaixo para configurar o EasyPanel como um container Docker:

```bash
sudo docker run --rm -it   -v /etc/easypanel:/etc/easypanel   -v /var/run/docker.sock:/var/run/docker.sock:ro   easypanel/easypanel setup
```

---

## 📋 Verifique se o EasyPanel está rodando

Verifique os containers em execução:

```bash
sudo docker ps
```

![Container do EasyPanel em execução](/img/install-easypanel-on-ubuntu-1.jpg)

---

## 🌐 Acesse o EasyPanel pelo navegador

Acesse no navegador:

```
http://<SEU_IP_VPS>:3000
```

Na primeira vez, será solicitado que você crie um **email e senha de admin**.

![Tela de login do EasyPanel](/img/install-easypanel-on-ubuntu-2.jpg)

---

## 🧭 Painel do EasyPanel

Após login, você verá o painel com opções para criar apps, gerenciar serviços, logs e mais.

![Dashboard do EasyPanel](/img/install-easypanel-on-ubuntu-3.jpg)

---

## ✅ Conclusão

Com o EasyPanel instalado, você pode **gerenciar seu VPS Ubuntu por uma interface moderna** usando containers Docker.  
Uma ótima ferramenta para ambientes de desenvolvimento, testes ou produção.

> Não se esqueça de proteger o painel com HTTPS usando um proxy reverso ou as ferramentas internas.
