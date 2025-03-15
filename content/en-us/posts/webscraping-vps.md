---
title: "Automating Candidate Call Monitoring with Web Scraping on a VPS"
date: 2025-03-15T12:00:00-03:00
slug: "webscraping-vps"
description: "Python web scraper implementation to monitor candidate calls on a website and send email notifications."
categories: ["Automation", "Python", "Web Scraping"]
tags: ["Python", "Web Scraping", "VPS", "Automation"]
image: "/img/webscraping-vps.jpg"
---

Many candidates need to constantly check institution websites for new calls. To automate this task, I developed a **Python web scraper** that looks for my name in the published documents and sends an email notification when a match is found.

## How does it work?
1. The script accesses the selection process website and extracts links from recent publications.
2. For each publication, it retrieves the PDF file link and downloads it.
3. The PDF text is extracted and searched for my name.
4. If my name is found, an email is sent with the document link.

## Project code
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
    """Loads already verified URLs and names from a JSON file."""
    if os.path.exists(CHECKED_URLS_FILE):
        with open(CHECKED_URLS_FILE, "r", encoding="utf-8") as file:
            return json.load(file)
    return {}

def save_checked_data(name, url):
    """Saves the URL and name to the JSON file."""
    checked_data = load_checked_data()
    
    if url not in checked_data:
        checked_data[url] = []  # Initializes the URL if it does not already exist
    
    if name not in checked_data[url]:
        checked_data[url].append(name)  # Add name to verified URL
    
    with open(CHECKED_URLS_FILE, "w", encoding="utf-8") as file:
        json.dump(checked_data, file, indent=4)

def is_checked(name, url):
    """Checks if the name has already been processed for the URL."""
    checked_data = load_checked_data()
    return url in checked_data and name in checked_data[url]

def get_publications_links(url):
    """Gets the links of the publications on the main page."""
    r = requests.get(url)
    page_content = r.content
    webpage = html.fromstring(page_content)
    xpath = '//div[contains(@class, "sb-integra-conteudo__arquivo")]//a/@href'
    return ['https://sebrae.com.br' + link for link in webpage.xpath(xpath)]


def get_file_link(publication_link):
    """Extracts the PDF file link from the publication link."""
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
    """Download the PDF file."""
    with open('download.pdf', 'wb') as file:
        try:
            content = requests.get(file_link, stream=True).content
        except MissingSchema:
            file_link = 'https://sebrae.com.br' + file_link[2:]
            print(f'Retentando: {file_link}')
            content = requests.get(file_link, stream=True).content
        file.write(content)

def extract_text_from_pdf():
    """Extract text from PDF."""
    reader = PdfReader('download.pdf')
    text = ' '.join(page.extract_text() for page in reader.pages)
    return text

def send_mail(text):
    """Send email if name is found in PDF."""
    YOUR_GOOGLE_EMAIL = ''  # The email you setup to send the email using app password
    YOUR_GOOGLE_EMAIL_APP_PASSWORD = ''  # The app password you generated

    smtpserver = smtplib.SMTP_SSL('smtp.gmail.com', 465)
    smtpserver.login(YOUR_GOOGLE_EMAIL, YOUR_GOOGLE_EMAIL_APP_PASSWORD)

    msg = EmailMessage()
    msg.set_content(text, 'html')
    msg['Subject'] = 'Candidate Call'
    msg['From'] = YOUR_GOOGLE_EMAIL
    msg['To'] = YOUR_GOOGLE_EMAIL

    print('Sending email...')
    smtpserver.send_message(msg)
    smtpserver.close()

# Name to be seached
name = 'your name'
url = 'https://sebrae.com.br/sites/PortalSebrae/ufs/ba/sebraeaz/comunicado%200012024-processo-seletivo-analista%20tecnico,44d9fe7ce5db0910VgnVCM1000001b00320aRCRD?vgnextrefresh=1'

message = ''

for publication_link in get_publications_links(url):
    if is_checked(name, publication_link):
        continue

    print(f'Checking: {publication_link}')
    try:
        file_link = get_file_link(publication_link)
        download_file(file_link)
    except ConnectionError:
        print('Connection Error')
        continue
    text = extract_text_from_pdf()

    if name.lower() in text.lower():
        message += f'<p><b>{name}</b> found in <a href="{file_link}">{file_link}</a></p>'
        print(f'{name} found in {file_link}')
    save_checked_data(name, publication_link) 
if message:
    send_mail(f'<html>{message}</html>')

```

This project is deployed and running automatically on my **VPS**, ensuring I never miss an important call.

---

Have you ever used **web scraping** to automate a task? Leave a comment! 🚀
