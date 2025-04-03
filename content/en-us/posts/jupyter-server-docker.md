---
title: "Running a Jupyter Notebook Server with Docker"
description: "Learn how to set up a local Jupyter Notebook server using Docker to streamline data science projects."
categories: ["Data Science"]
tags: ["Jupyter", "Docker", "Data Science", "Python"]
slug: "jupyter-server-docker"
date: "2025-04-03T12:00:00-03:00"
image: "/img/jupyter-server-docker.jpg"
---

If you study or work with **Data Science in Python**, Jupyter Notebook is an essential tool. In this tutorial, we will set up a **Jupyter Notebook server** using **Docker**, providing a ready-to-use environment without complex manual configurations.

---

## Requirements

To reproduce this environment, you must have **Docker Engine** installed on your computer.  
To install Docker, follow the instructions for your operating system here: [Install Docker](https://docs.docker.com/engine/install/).

---

## Step 1: Create the Folder Structure

Open a terminal and run:

```bash
mkdir -p ~/dev/docker/jupyter-server/work
cd ~/dev/docker/jupyter-server
```

---

## Step 2: Create the `Dockerfile`

We will create a `Dockerfile` based on the **minimal-notebook** Jupyter image. Other options are available [here](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/selecting.html).

Create a `Dockerfile` with the following content:

```dockerfile
FROM jupyter/minimal-notebook

COPY ./requirements.txt /tmp/requirements.txt
RUN pip install -r /tmp/requirements.txt
```

---

## Step 3: Create the `requirements.txt`

List the required Python libraries for your projects in `requirements.txt`:

```text
numpy==2.2.4
pandas==2.2.3
openpyxl==3.1.5
matplotlib==3.10.1
```

---

## Step 4: Create the `docker-compose.yml`

The `docker-compose.yml` file simplifies service configuration. The `work` directory will be used to store notebooks.

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

## Step 5: Start the Server

Run the container:

```bash
docker compose up -d
```

---

![Loaded URL on the browser](/img/jupyter-server-docker-1.jpg)

## Step 6: Access the Server

To get the access URL, run:

```bash
docker logs jupyter-server 2>&1 | grep "127." | tail -1
```

Copy the displayed URL and paste it into your browser, example:

```
http://127.0.0.1:8888/lab?token=e8d99ec57344fc92060a598a5ae6656c5f162aa4511dc0da
```

---

![Loaded URL on the browser](/img/jupyter-server-docker-2.jpg)

## Tip: Using Jupyter in VSCode

If you use **VSCode**, you can connect to the Jupyter server to run notebooks directly in the IDE. Just enter the **Jupyter URL** when running notebook cells for the first time.

---

![Configurar servidor Jupyter no VSCode](/img/jupyter-server-docker-3.jpg)

![Configurar servidor Jupyter no VSCode](/img/jupyter-server-docker-4.jpg)

## Step 7: Stop the Server

To stop the Jupyter Notebook container:

```bash
docker compose down
```

Now, you have a **Jupyter Notebook** environment set up with Docker, ready for **Data Science** projects! 🎯
