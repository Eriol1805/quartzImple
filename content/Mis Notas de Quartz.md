---
type: Quartz
language: Js
tags:
  - codigo
  - obsidian
  - quartz
  - git
  - github
---

## Problema
Ante la necesidad de compartir notas de Obsidian surge esta herramienta que permite la publicación de determinado contenido directamente en una pagina de GitHub Pages

## Solución
 - Documentación:
	 https://quartz.jzhao.xyz/

1. Clonamos el repo en local y podemos ver la pagina en localhost
``` bash

# En un fichero que desees compartir publicamente

git clone https://github.com/jackyzha0/quartz.git
cd quartz
npm i
npx quartz create


npx quartz build --serve #http://localhost:8080

```

2. Temas (Opcional)
	https://github.com/saberzero1/quartz-themes

``` bash
# Ejemple de aplicacion de tema en Windows
curl -s -S -o action.bat https://raw.githubusercontent.com/saberzero1/quartz-themes/master/action.bat

action.bat blue-topaz
```

3. Alojamiento en Github

Quarts es un generador de sitios estáticos. 

- Creamos un repositorio *publico* en github

``` bash
git remote -v #veremos que está conectado al repo de Quartz

git remote set-url origin REMOTE-URL #Aquí ponemos el HTTPS de nuestro repo

npx quartz sync --no-pull #Solo la primera vez para sincronizar

npx quartz sync #Luego solo este para sicronizar
```

- Configuramos el archivo deploy.yml
	Este le dará a Github las instrucciones para el deploy mediante github actions

>.github/workflows/deploy.yml

``` bash
name: Deploy Quartz site to GitHub Pages
 
on:
  push:
    branches:
      - main
 
permissions:
  contents: read
  pages: write
  id-token: write
 
concurrency:
  group: "pages"
  cancel-in-progress: false
 
jobs:
  build:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Fetch all history for git info
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - name: Install Dependencies
        run: npm ci
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public
 
  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- Implementar los cambios

``` bash
npx quartz sync

git push -u origin main

```

- En github pages:

Build and deployment

`GitHubActions`