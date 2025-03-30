---
title: "Como criar um chatbot automatizado de WhatsApp com N8N no Docker"
description: "Um guia passo a passo para configurar um chatbot automatizado do WhatsApp usando N8N e Docker."
categories: ["Automação", "Chatbot"]
tags: ["Python", "Docker", "N8N", "Automação", "Chatbot"]
slug: "chatbot-whatsapp-n8n"
date: "2025-03-30T12:00:00-03:00"
image: "/img/chatbot-whatsapp-n8n.jpg"
---

## Criando um chatbot automatizado de WhatsApp com N8N no Docker

Neste guia, vamos configurar um chatbot automatizado do WhatsApp usando **N8N** e **Docker**. Com esse fluxo, o chatbot poderá receber e responder mensagens no WhatsApp utilizando o módulo **WAHA**.

---

## Passo 1: Criar a estrutura do projeto
```bash
mkdir -p ~/dev/docker/n8n
cd ~/dev/docker/n8n
mkdir n8n_data local_files waha_tmp waha_sessions
```

---

## Passo 2: Criar o arquivo `docker-compose.yml`
Crie um arquivo `docker-compose.yml` e adicione a seguinte configuração:
```yaml
services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    ports:
      - "5678:5678"
    environment:
      - WEBHOOK_URL=http://host.docker.internal:5678
      - GENERIC_TIMEZONE=America/Bahia
    volumes:
      - ./n8n_data:/home/node/.n8n
      - ./local_files:/files
```

---

## Passo 3: Rodar o container
```bash
docker compose up -d
```
Acesse o painel do N8N em: [http://localhost:5678/](http://localhost:5678/)

---

## Passo 4: Instalar a extensão WAHA
- Acesse **Settings > Community Nodes**
- Clique em **Install a community node**
- Preencha o nome do pacote: `@devlikeapro/n8n-nodes-waha`
- Clique em **Install**

![Instalar extensão WAHA](/img/chatbot-whatsapp-n8n-1.jpg)

---

## Passo 5: Inserir o componente WAHA Webhook
- Na página inicial, clique em **Start from scratch**
- Clique no botão **+** e pesquise por `Waha Trigger`
- No painel do componente, copie a **Webhook URL**

![Inserir WAHA Webhook](/img/chatbot-whatsapp-n8n-2.jpg)

---

## Passo 6: Adicionar o serviço WAHA ao `docker-compose.yml`
Edite o `docker-compose.yml` e adicione:
```yaml
  waha:
    image: devlikeapro/waha
    ports:
      - "3000:3000"
    environment:
      - WHATSAPP_DEFAULT_ENGINE=NOWEB
      - WHATSAPP_HOOK_EVENTS=message
      - WHATSAPP_HOOK_URL=http://n8n:5678/webhook-test/{SUA-WEBHOOK-URL}/waha
    volumes:
      - ./waha_tmp:/tmp
      - ./waha_sessions:/app/.sessions
```
Substitua o valor de **WHATSAPP_HOOK_URL** pela **Webhook URL** copiada no passo anterior e substitua o domínio pelo nome do container **n8n**

Reinicie os containers:
```bash
docker compose up -d
```
Acesse o painel WAHA: [http://localhost:3000/dashboard/](http://localhost:3000/dashboard/)

---

## Passo 7: Conectar WAHA ao WhatsApp
1. No painel do WAHA, vá para **Sessions**
2. Clique em **Start** na linha `default`
3. Clique em **Login** para gerar o QR Code
4. No celular, acesse o WhatsApp e escaneie o QR Code
5. Ainda na linha `default` vá em clique em **Configuration**, desça a página até  **Webhooks**, clique em **+Webhook** e adicione a URL do Webhook do N8N copiada a dois passos atrás
6. Clique em **Update**

![Conectar WAHA ao WhatsApp](/img/chatbot-whatsapp-n8n-3.jpg)

---

## Passo 8: Testar o Webhook
1. No N8N, clique em **Test step** dentro do componente **Waha Trigger**
2. Envie uma mensagem pelo WhatsApp
3. O N8N exibirá os dados recebidos
4. Clique em **Message Brach(1 item)** para ver os dados

![Testar Webhook](/img/chatbot-whatsapp-n8n-4.jpg)

---

## Passo 9: Processar mensagens recebidas
1. Adicione o componente **Edit Fields (Set)**
2. Configure os seguintes campos:
   - `session`: `json.session`
   - `from`: `json.payload.from`
   - `message`: `json.payload.body`
   - `message_id`: `json.payload.id`

![Processar mensagens](/img/chatbot-whatsapp-n8n-5.jpg)

---

## Passo 10: Enviar confirmação de leitura (✔✔ Azul)
1. Clique na ramificação de **Edit Fields (Set)** e adicione o componente **WAHA Send Seen**
2. Crie uma credencial no campo **Credential to connect with** e defina o **Host URL** como `http://waha:3000`
3. Configure os campos:
   - `Session`: `json.session`
   - `Chat Id`: `json.from`
   - `Message Id`: `json.message_id`
   - `Participant`: (Deixe vazio)

![Enviar confirmação de leitura](/img/chatbot-whatsapp-n8n-6.jpg)

---

## Passo 11: Enviar resposta automatizada
1. Clique na ramificação de **Send Seen** e adicione o componente **WAHA Send a text message**
2. Configure os campos:
   - `Session`: `json.session`
   - `Chat Id`: `json.from`
   - `Text`: `PingPong: json.message`

![Enviar resposta automática](/img/chatbot-whatsapp-n8n-7.jpg)

---

## Passo 12: Executar o fluxo completo
1. Desligue os containers: `docker compose down`
2. Substitua o `WHATSAPP_HOOK_URL` do `docker-compose.yml` pela **Webhook URL** de produção
3. Reinicie os containers: `docker compose up -d`
4. No N8N, ative o workflow
5. No painel do WAHA, clique em **Start**, depois vá em **Configuration** para atualizar o **Webhook URL** de produção
6. No N8N, clique em **Executions** para ver o workflow quando for executado
7. Teste enviando uma mensagem no WhatsApp para ver o fluxo executando

![Fluxo completo](/img/chatbot-whatsapp-n8n-8.jpg)

---

## Conclusão
Agora você tem um chatbot automatizado no WhatsApp utilizando N8N e WAHA. Esse fluxo pode ser expandido para responder comandos específicos, interagir com bancos de dados e muito mais!

![Resultado final](/img/chatbot-whatsapp-n8n-9.jpg)
