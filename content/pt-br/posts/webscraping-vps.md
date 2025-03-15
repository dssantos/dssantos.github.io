---
title: "Automatizando o Monitoramento de Convocações de Concursos com Web Scraping em uma VPS"
date: 2025-03-15T12:00:00-03:00
slug: "webscraping-vps"
description: "Implementação de um web scraper em Python para monitorar convocações em um site e enviar notificações por e-mail."
categories: ["Automação", "Python", "Web Scraping"]
tags: ["Python", "Web Scraping", "VPS", "Automação"]
image: "/img/webscraping-vps.jpg"
---

Muitos candidatos aprovados em processos seletivos precisam monitorar constantemente os sites das instituições para verificar novas convocações. Para automatizar essa tarefa, desenvolvi um **web scraper em Python** que busca meu nome nos documentos publicados e envia um e-mail quando há uma correspondência.

## Como funciona?
1. O script acessa o site do processo seletivo e extrai os links das publicações mais recentes.
2. Para cada publicação, ele obtém o link do arquivo PDF e faz o download.
3. O texto do PDF é extraído e pesquisado pelo meu nome.
4. Se meu nome for encontrado, um e-mail é enviado com o link do documento.

## Código do projeto
```python
import os
import json
import requests
from lxml import html
from pypdf import PdfReader
from requests.exceptions import MissingSchema, ConnectionError
import smtplib
from email.message import EmailMessage

CHECKED_URLS_FILE = "checked_urls.json"

def load_checked_data():
    """Carrega as URLs e nomes já verificados de um arquivo JSON."""
    if os.path.exists(CHECKED_URLS_FILE):
        with open(CHECKED_URLS_FILE, "r", encoding="utf-8") as file:
            return json.load(file)
    return {}

def save_checked_data(name, url):
    """Salva a URL e o nome no arquivo JSON."""
    checked_data = load_checked_data()
    
    if url not in checked_data:
        checked_data[url] = []  # Inicializa a URL se ainda não existir
    
    if name not in checked_data[url]:
        checked_data[url].append(name)  # Adiciona o nome à URL verificada
    
    with open(CHECKED_URLS_FILE, "w", encoding="utf-8") as file:
        json.dump(checked_data, file, indent=4)

def is_checked(name, url):
    """Verifica se o nome já foi processado para a URL."""
    checked_data = load_checked_data()
    return url in checked_data and name in checked_data[url]

def get_publications_links(url):
    """Obtém os links das publicações na página principal."""
    r = requests.get(url)
    page_content = r.content
    webpage = html.fromstring(page_content)
    xpath = '//div[contains(@class, "sb-integra-conteudo__arquivo")]//a/@href'
    return ['https://sebrae.com.br' + link for link in webpage.xpath(xpath)]


def get_file_link(publication_link):
    """Extrai o link do arquivo PDF a partir do link da publicação."""
    r = requests.get(publication_link)
    download_webpage = html.fromstring(r.content)
    xpath = '//input[@id="urlDownload"]/@value'
    try:
        file_link = download_webpage.xpath(xpath)[0]
    except IndexError:
        r = requests.get(url)
        download_webpage = html.fromstring(r.content)

    return download_webpage.xpath(xpath)[0]

def download_file(file_link):
    """Baixa o arquivo PDF."""
    with open('download.pdf', 'wb') as file:
        try:
            content = requests.get(file_link, stream=True).content
        except MissingSchema:
            file_link = 'https://sebrae.com.br' + file_link[2:]
            print(f'Retentando: {file_link}')
            content = requests.get(file_link, stream=True).content
        file.write(content)

def extract_text_from_pdf():
    """Extrai texto do PDF."""
    reader = PdfReader('download.pdf')
    text = ' '.join(page.extract_text() for page in reader.pages)
    return text

def send_mail(text):
    """Envia email se o nome for encontrado no PDF."""
    YOUR_GOOGLE_EMAIL = ''  # O e-mail que você configurou para enviar o e-mail usando a senha do aplicativo
    YOUR_GOOGLE_EMAIL_APP_PASSWORD = ''  # A senha do aplicativo que você gerou

    smtpserver = smtplib.SMTP_SSL('smtp.gmail.com', 465)
    smtpserver.login(YOUR_GOOGLE_EMAIL, YOUR_GOOGLE_EMAIL_APP_PASSWORD)

    msg = EmailMessage()
    msg.set_content(text, 'html')
    msg['Subject'] = 'Convocação SEBRAE'
    msg['From'] = YOUR_GOOGLE_EMAIL
    msg['To'] = YOUR_GOOGLE_EMAIL

    print('Enviando email...')
    smtpserver.send_message(msg)
    smtpserver.close()

# Nome a ser pesquisado
name = 'seu nome'
url = 'https://sebrae.com.br/sites/PortalSebrae/ufs/ba/sebraeaz/comunicado%200012024-processo-seletivo-analista%20tecnico,44d9fe7ce5db0910VgnVCM1000001b00320aRCRD?vgnextrefresh=1'

message = ''

for publication_link in get_publications_links(url):
    if is_checked(name, publication_link):
        continue

    print(f'Checando: {publication_link}')
    try:
        file_link = get_file_link(publication_link)
        download_file(file_link)
    except ConnectionError:
        print('Erro de conexão')
        continue
    text = extract_text_from_pdf()

    if name.lower() in text.lower():
        message += f'<p><b>{name}</b> encontrado em <a href="{file_link}">{file_link}</a></p>'
        print(f'{name} encontrado em {file_link}')
    save_checked_data(name, publication_link) 
if message:
    send_mail(f'<html>{message}</html>')

```

Este projeto foi implantado e está rodando automaticamente em minha **VPS**, garantindo que eu nunca perca uma convocação importante.

---

Você já utilizou **web scraping** para automatizar alguma tarefa? Deixe um comentário! 🚀
