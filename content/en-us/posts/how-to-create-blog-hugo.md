---
title: "How to Create a Blog with Hugo"
date: 2025-02-01T00:00:00-03:00
categories: ["Web Development"]
tags: ["Hugo", "Blog", "GitHub Pages", "Markdown"]
slug: "how-to-create-blog-hugo"
image: "/img/hugo-blog.jpg"
description: "Learn how to set up and publish a blog using Hugo and GitHub Pages."
---

Hugo is a fast and flexible static site generator, ideal for creating blogs and personal websites. In this guide, you will learn how to set up and publish a blog using Hugo and GitHub Pages.

## 1. Installing Hugo

If you use Linux, you can install Hugo with the following command:
```bash
sudo apt install hugo
```
For other operating systems, check the [official documentation](https://gohugo.io/getting-started/installing/).

## 2. Creating the Project

Choose a directory where you want to store your blog and run the following commands:
```bash
mkdir -p ~/dev/hugo
cd ~/dev/hugo
hugo new site blog
cd blog
```

## 3. Configuring Git and the Theme

Initialize the Git repository:
```bash
git init
```
Choose a theme from the [theme gallery](https://themes.gohugo.io/) and install it as a Git submodule:
```bash
git submodule add https://github.com/rhazdon/hugo-theme-hello-friend-ng.git themes/hello-friend-ng
```
Set the theme in the `hugo.toml` file:
```bash
echo "theme = 'hello-friend-ng'" >> hugo.toml
```

## 4. Running the Local Server

To preview your blog locally, run:
```bash
hugo server -D
```
Access it in your browser: [http://localhost:1313/](http://localhost:1313/).

## 5. Creating a Post

Add a new post:
```bash
hugo new content/en-us/posts/hello-world.md
```
Edit the generated file (`content/en-us/posts/hello-world.md`) to customize the content.

## 6. Creating the Post Menu
Edit the `hugo.toml` file and add the following lines to create the site's menu:
```bash
defaultContentLanguage = 'en-us'

[languages]
  [languages.en-us]
    contentDir = 'content/en-us'
    disabled = false
    languageCode = 'en-US'
    weight = 1
    [languages.en-us.menus]
      [[languages.en-us.menus.main]]
        name = 'Posts'
        pageRef = '/posts'
        weight = 1
```

## 7. Publishing on GitHub Pages

Create a GitHub repository named `<YOUR_USERNAME>.github.io` and push the project:
```bash
git remote add origin https://github.com/<YOUR_USERNAME>/<YOUR_USERNAME>.github.io.git
git branch -M main
git add .
git commit -m "first commit"
git push -u origin main
```

On GitHub:
1. Go to *Settings* > *Pages* and set the *Source* to 'GitHub Actions'.

Create the `.github/workflows/hugo.yaml` file to automate publishing. Check an example configuration [here](https://gohugo.io/hosting-and-deployment/hosting-on-github/#procedure).

Then, publish the workflow:
```bash
git add .
git commit -m "create github workflow"
git push
```

On GitHub:

1. Go to *Actions* and run the workflow.
2. Access `<YOUR_USERNAME>.github.io` to view your blog online.

## 8. Publishing the Post

To make the post visible in production, edit the post file and change the `draft` parameter to `false`:
```
draft = false
```

## 9. Customization (Optional)

- Edit `hugo.toml` to adjust settings like title and metadata.
- [Add a language switcher button](https://dev.to/feministclickback/how-to-add-a-language-switcher-to-your-hugo-site-2oh6).
- Try different themes and layouts to personalize your blog.

Now you have a fully functional blog using Hugo! 🚀
