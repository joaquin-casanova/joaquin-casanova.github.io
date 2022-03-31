---
title: "Makefile"
summary: Optimiza tu tiempo con Makefile
date: 2022-03-30
weight: 1
aliases: ["/papermod-features"]
tags: ["Data Engineering"]
author: "Joaquin C"
draft: false
---

### Introducción
Hace algunos días comencé con la lectura de **THE EFFECTIVE ENGINEER** y una de las frases que más movió fue la siguiente:

#### THE EFFECTIVE ENGINEER

> Focus your effort on leverage points.
>
>"Time is your most limited asset. Identify the habits that produce disproportionately high impact for the time you invest."
> — <cite>Edmond Lau[^1]</cite>

[^1]: La frase anterior es un extracto de THE EFFECTIVE ENGINEER book [link](https://www.amazon.com.mx/Effective-Engineer-Engineering-Disproportionate-Meaningful/dp/0996128107).

### Instrospeción
Lo anterior me llevó a cuestionarme como podría optimizar mi tiempo en el trabajo, y en esa busqueda me dí cuenta que cuando trabajo en la generación de algún DAG en Airflow normalmente utilizo la misma serie de comandos:



>-   Iniciar en contenedor:
>      -   `astro dev start`
>-   Detener el contenedor:
>       -   `astro dev stop`
>-   Revisar linter (Flake8, PyDocStyle y MyPY):
>       -   `./development/tools/build.sh`
>       -   `./development/tools/linter.sh`
>-   Revisar los logs del scheduler:
>       - 	`docker exec -it <container_id> /bin/bash`

bien se podría pensar que no son una cantidad exagerada de comandos, sin embargo frecuentemente me enfrentaba a los siguientes escenarios:
>-  Teclear mal alguna letra de los comandos.
>-  Olvidar el orden de las sentencias.
>-  Olvidar darle start cuando le daba stop.
>-  Ir directo al linter sin realizar el build

Si bien esto no era todos los días y no todos los comandos este tema si me representaba un estres innecesario en mi día de trabajo además de que sumando mi tiempo perdido y el de mis compañeros (suponiendo que son propensos a los mismos errores), esto se vuelve relevante debido a que es el tiempo de todo un equipo el que se pierde y no se aprovecha en lo que realmente genera valor. 
### Makefile
Lo único que necesitas es crear un archivo llamado Makefile
```shell
$ touch Makefile
```
una vez creado es cuestión de agregar los comando bash que más utilices y desees automatizar mediante alguna bandera, a continuación te muestro el makefile que utilizo:
#### Ejemplo de mi actual Makefile
```shell
help:
	@echo "available commands"
	@echo " - start    	: start Airflow"
	@echo " - stop      	: stop Airflow"
	@echo " - build     	: build the imagen"
	@echo " - linter    	: check the linter (Flake8, PyDocStyle and MyPY)"
	@echo " - check     	: runs all checks (tests + style)"
	@echo " - fdev		: update the current branch with develop"
	@echo " - logs		: monitor scheduler logs"
	@echo " - term		: open scheduler terminal"

start:
	astro dev start

stop:
	astro dev stop

restart: stop start

build:
	./development/tools/build.sh

linter:
	./development/tools/linter.sh

fdev:
	git fetch origin develop:develop && git merge develop

logs:
	docker logs --follow `docker ps --filter name=scheduler --format {{.ID}}`

term:
	docker exec -it `docker ps --filter name=scheduler --format {{.ID}}` bash

check: build linter
```

Una vez creado el documento, si tecleas el siguiente comando:
```shell
$ make help
```
 obtendras el siguiente resultado:
```shell
available commands
 - start        : start Airflow
 - stop         : stop Airflow
 - build        : build the imagen
 - linter       : check the linter (Flake8, PyDocStyle and MyPY)
 - check        : runs all checks (tests + style)
 - fdev         : update the current branch with develop
 - logs         : monitor scheduler logs
 - term         : open scheduler terminal
 ```

 ### Conclusión
 Hasta ahora llevo apróximadamente un mes con la implementación anterior y no me he vuelto a equivocar además
 de que mis compañeros del equipo se han adaptado y agregado sus propios comandos.