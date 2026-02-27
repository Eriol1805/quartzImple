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