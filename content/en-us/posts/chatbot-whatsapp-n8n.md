---
title: "How to Create an Automated WhatsApp Chatbot with N8N on Docker"
description: "A step-by-step guide to setting up an automated WhatsApp chatbot using N8N and Docker."
categories: ["Automation", "Chatbot"]
tags: ["Python", "Docker", "N8N", "Automation", "Chatbot"]
slug: "chatbot-whatsapp-n8n"
date: "2025-03-30T12:00:00-03:00"
image: "/img/chatbot-whatsapp-n8n.jpg"
---

## Creating an Automated WhatsApp Chatbot with N8N on Docker

In this guide, we will set up an automated WhatsApp chatbot using **N8N** and **Docker**. This workflow will allow the bot to receive and respond to WhatsApp messages using the **WAHA** module.

---

## Step 1: Create the Project Structure
```bash
mkdir -p ~/dev/docker/n8n
cd ~/dev/docker/n8n
mkdir n8n_data local_files waha_tmp waha_sessions
```

---

## Step 2: Create the `docker-compose.yml` File
Create a `docker-compose.yml` file and add the following configuration:
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

## Step 3: Run the Container
```bash
docker compose up -d
```
Access the N8N panel at: [http://localhost:5678/](http://localhost:5678/)

---

## Step 4: Install the WAHA Extension
- Go to **Settings > Community Nodes**
- Click **Install a community node**
- Enter the package name: `@devlikeapro/n8n-nodes-waha`
- Click **Install**

![Install WAHA Extension](/img/chatbot-whatsapp-n8n-1.jpg)

---

## Step 5: Insert the WAHA Webhook Component
- On the homepage, click **Start from scratch**
- Click the **+** button and search for `Waha Trigger`
- In the component panel, copy the **Webhook URL**

![Insert WAHA Webhook](/img/chatbot-whatsapp-n8n-2.jpg)

---

## Step 6: Add WAHA Service to `docker-compose.yml`
Edit `docker-compose.yml` and add:
```yaml
  waha:
    image: devlikeapro/waha
    ports:
      - "3000:3000"
    environment:
      - WHATSAPP_DEFAULT_ENGINE=NOWEB
      - WHATSAPP_HOOK_EVENTS=message
      - WHATSAPP_HOOK_URL=http://n8n:5678/webhook-test/{YOUR-WEBHOOK-URL}/waha
    volumes:
      - ./waha_tmp:/tmp
      - ./waha_sessions:/app/.sessions
```
Replace **WHATSAPP_HOOK_URL** with the copied **Webhook URL** and change the domain to **n8n**.

Restart the containers:
```bash
docker compose up -d
```
Access the WAHA panel: [http://localhost:3000/dashboard/](http://localhost:3000/dashboard/)

---

## Step 7: Link WAHA to WhatsApp
1. In the WAHA panel, go to **Sessions**
2. Click **Start** on the `default` row
3. Click **Login** to generate the QR Code
4. On your phone, open WhatsApp and scan the QR Code
5. Still on the `default` row, click **Configuration**, scroll down to **Webhooks**, click **+Webhook**, and add the Webhook URL from N8N
6. Click **Update**

![Link WAHA to WhatsApp](/img/chatbot-whatsapp-n8n-3.jpg)

---

## Step 8: Test the Webhook
1. In N8N, click **Test step** inside the **Waha Trigger** component
2. Send a message via WhatsApp
3. N8N will display the received data
4. Click **Message Branch(1 item)** to view the details

![Test Webhook](/img/chatbot-whatsapp-n8n-4.jpg)

---

## Step 9: Parse Received Messages
1. Add the **Edit Fields (Set)** component
2. Configure the following fields:
   - `session`: `json.session`
   - `from`: `json.payload.from`
   - `message`: `json.payload.body`
   - `message_id`: `json.payload.id`

![Parse Messages](/img/chatbot-whatsapp-n8n-5.jpg)

---

## Step 10: Send Read Confirmation (✔✔ Blue Checks)
1. Click the **Edit Fields (Set)** branch and add the **WAHA Send Seen** component
2. Create a credential under **Credential to connect with**, setting **Host URL** to `http://waha:3000`
3. Configure the fields:
   - `Session`: `json.session`
   - `Chat Id`: `json.from`
   - `Message Id`: `json.message_id`
   - `Participant`: (Leave empty)

![Send Read Confirmation](/img/chatbot-whatsapp-n8n-6.jpg)

---

## Step 11: Send an Automated Response
1. Click the **Send Seen** branch and add the **WAHA Send a text message** component
2. Configure the fields:
   - `Session`: `json.session`
   - `Chat Id`: `json.from`
   - `Text`: `PingPong: json.message`

![Send Automated Response](/img/chatbot-whatsapp-n8n-7.jpg)

---

## Step 12: Run the Complete Workflow
1. Stop the containers: `docker compose down`
2. Replace `WHATSAPP_HOOK_URL` in `docker-compose.yml` with the **production Webhook URL**
3. Restart the containers: `docker compose up -d`
4. In N8N, activate the workflow
5. In WAHA, click **Start** in the default session, then go to **Configuration** to update the **Webhook URL**
6. In N8N, click **Executions** to see the workflow running
7. Test by sending a WhatsApp message to see the execution in action

![Complete Workflow](/img/chatbot-whatsapp-n8n-8.jpg)

---

## Conclusion
Now you have an automated WhatsApp chatbot using N8N and WAHA. This workflow can be expanded to respond to specific commands, integrate with databases, and much more!

![Final Result](/img/chatbot-whatsapp-n8n-9.jpg)
