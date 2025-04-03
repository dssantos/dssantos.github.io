---
title: "Executando um Servidor Jupyter Notebook com Docker"
description: "Aprenda a configurar um servidor Jupyter Notebook localmente usando Docker para facilitar projetos de ciência de dados."
categories: ["Ciência de Dados"]
tags: ["Jupyter", "Docker", "Data Science", "Python"]
slug: "jupyter-server-docker"
date: "2025-04-03T03:00:00-03:00"
image: "/img/jupyter-server-docker.jpg"
---



Se você estuda ou trabalha com **Ciência de Dados em Python**, o Jupyter Notebook é uma ferramenta essencial. Neste tutorial, vamos configurar um servidor **Jupyter Notebook** usando **Docker**, garantindo um ambiente pronto para uso sem necessidade de configurações manuais complexas.

---

## Requisitos

Para reproduzir este ambiente, você precisa ter o **Docker Engine** instalado em seu computador.  
Para instalar o Docker, siga as instruções para seu sistema operacional aqui: [Instalar Docker](https://docs.docker.com/engine/install/).

---

## Passo 1: Criar a Estrutura de Diretórios

Abra o terminal e execute:

```bash
mkdir -p ~/dev/docker/jupyter-server/work
cd ~/dev/docker/jupyter-server
```

---

## Passo 2: Criar o `Dockerfile`

Vamos criar um `Dockerfile` baseado na imagem **minimal-notebook** do Jupyter. Outras opções estão disponíveis [aqui](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/selecting.html).

Crie um arquivo `Dockerfile` com o seguinte conteúdo:

```dockerfile
FROM jupyter/minimal-notebook

COPY ./requirements.txt /tmp/requirements.txt
RUN pip install -r /tmp/requirements.txt
```

---

## Passo 3: Criar o `requirements.txt`

Liste as bibliotecas Python necessárias para seus projetos no arquivo `requirements.txt`:

```text
numpy==2.2.4
pandas==2.2.3
openpyxl==3.1.5
matplotlib==3.10.1
```

---

## Passo 4: Criar o `docker-compose.yml`

O `docker-compose.yml` permite configurar o serviço de forma simplificada. O diretório `work` será usado para armazenar os notebooks.

```yaml
services:
  jupyter:
    container_name: jupyter-server
    build: 
      context: .
    user: root
    volumes:
      - ./work:/home/jovyan/work
    ports:
      - 8888:8888
    command: "start-notebook.py --ServerApp.root_dir=/home/jovyan/work"
```

---

## Passo 5: Executar o Servidor

Agora, basta rodar o container:

```bash
docker compose up -d
```

![Loaded URL on the browser](/img/jupyter-server-docker-1.jpg)

---

## Passo 6: Acessar o Servidor

Para obter a URL de acesso, execute:

```bash
docker logs jupyter-server 2>&1 | grep "127." | tail -1
```

Copie a URL exibida e cole no navegador, exemplo:

```
http://127.0.0.1:8888/lab?token=e8d99ec57344fc92060a598a5ae6656c5f162aa4511dc0da
```

---

![URL carregada no navegador](/img/jupyter-server-docker-2.jpg)

## Dica: Usando o Jupyter no VSCode

Se você usa **VSCode**, pode conectar-se ao servidor Jupyter para rodar notebooks diretamente na IDE. Basta inserir a **URL do Jupyter** quando executar células do notebook pela primeira vez.

---

![Configurar servidor Jupyter no VSCode](/img/jupyter-server-docker-3.jpg)

![Configurar servidor Jupyter no VSCode](/img/jupyter-server-docker-4.jpg)

## Passo 7: Parar o Servidor

Para parar o container Jupyter Notebook:

```bash
docker compose down
```

Agora, você tem um ambiente de **Jupyter Notebook** configurado no Docker, pronto para projetos de **Ciência de Dados**! 🎯
