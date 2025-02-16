---
title: "Como Criar um Blog com Hugo"
date: 2025-02-01
categories: ["Desenvolvimento Web"]
tags: ["Hugo", "Blog", "GitHub Pages", "Markdown"]
slug: "como-criar-blog-hugo"
image: "/img/hugo-blog.jpg"
---

Hugo é um gerador de sites estáticos rápido e flexível, ideal para criar blogs e sites pessoais. Neste guia, você aprenderá a configurar e publicar um blog utilizando Hugo e GitHub Pages.

## 1. Instalando o Hugo

Se você utiliza Linux, pode instalar o Hugo com o seguinte comando:
```bash
sudo apt install hugo
```
Para outros sistemas operacionais, consulte a [documentação oficial](https://gohugo.io/getting-started/installing/).

## 2. Criando o Projeto

Escolha um diretório onde deseja armazenar o blog e execute os seguintes comandos:
```bash
mkdir -p ~/dev/hugo
cd ~/dev/hugo
hugo new site blog
cd blog
```

## 3. Configurando o Git e o Tema

Inicialize o repositório Git:
```bash
git init
```
Escolha um tema na [galeria de temas](https://themes.gohugo.io/) e instale-o como submódulo Git:
```bash
git submodule add https://github.com/rhazdon/hugo-theme-hello-friend-ng.git themes/hello-friend-ng
```
Defina o tema no arquivo `hugo.toml`:
```bash
echo "theme = 'hello-friend-ng'" >> hugo.toml
```

## 4. Rodando o Servidor Local

Para visualizar o blog localmente, execute:
```bash
hugo server -D
```
Acesse no navegador: [http://localhost:1313/](http://localhost:1313/).

## 5. Criando uma Publicação

Adicione um novo post:
```bash
hugo new content/pt-br/posts/hello-world.md
```
Edite o arquivo gerado (`content/pt-br/posts/hello-world.md`) para personalizar o conteúdo.

## 6. Criando o menu dos posts
Edite o arquivo (`hugo.toml`) e adicione as linhas abaixo para criar o menu do site.
```bash
defaultContentLanguage = 'pt-br'

[languages]
  [languages.pt-br]
    contentDir = 'content/pt-br'
    disabled = false
    languageCode = 'pt-BR'
    weight = 1
    [languages.pt-br.menus]
      [[languages.pt-br.menus.main]]
        name = 'Posts'
        pageRef = '/posts'
        weight = 1
```

## 7. Publicando no GitHub Pages

Crie um repositório no GitHub com o nome `<SEU_USUARIO>.github.io` e envie o projeto:
```bash
git remote add origin https://github.com/<SEU_USUARIO>/<SEU_USUARIO>.github.io.git
git branch -M main
git add .
git commit -m "first commit"
git push -u origin main
```

No GitHub:
1. Vá em *Settings* > *Pages* e defina o *Source* como 'GitHub Actions'.

Crie o arquivo `.github/workflows/hugo.yaml` para automatizar a publicação. Veja um exemplo de configuração [aqui](https://gohugo.io/hosting-and-deployment/hosting-on-github/#procedure).

Em seguida publique o workflow:
```bash
git add .
git commit -m "creta github workflow"
git push
```

No GitHub:

1. Vá em *Actions* e execute o workflow.
2. Acesse `<SEU_USUARIO>.github.io` para visualizar seu blog online.

## 8. Publicando o post
Para visualizar o post em produção edite o arquivo do post e modifique o parametro `draft` para `false`:
```
draft = false
```

## 9. Personalização (Opcional)

- Edite `hugo.toml` para ajustar configurações como título e metadados.
- [Adicione um botão de troca de idioma](https://dev.to/feministclickback/how-to-add-a-language-switcher-to-your-hugo-site-2oh6).
- Experimente diferentes temas e layouts para personalizar seu blog.

Agora você tem um blog funcional utilizando Hugo! 🚀

