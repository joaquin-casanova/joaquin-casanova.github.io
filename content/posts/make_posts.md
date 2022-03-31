---
title: "Makefile"
summary: Time is your most limited asset
date: 2022-03-30
weight: 1
aliases: ["/papermod-features"]
tags: ["Data Engineering"]
author: "Joaquin C"
draft: false
cover:
    image: "/posts/make_help.png" # image path/url
    alt: "make help" # alt text
    caption: "make help" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

### Introducción
A few days ago I started reading **THE EFFECTIVE ENGINEER** and one of the main sentences that shock me was the following:

#### THE EFFECTIVE ENGINEER

> Focus your effort on leverage points.
>
>"Time is your most limited asset. Identify the habits that produce disproportionately high impact for the time you invest."
> — <cite>Edmond Lau[^1]</cite>

[^1]: The above quote is excerpted from THE EFFECTIVE ENGINEER book [link](https://www.amazon.com.mx/Effective-Engineer-Engineering-Disproportionate-Meaningful/dp/0996128107).

### Introspection
This led me to ask myself, How I can optimize my time? And I discover that when I work with Airflow, I frequently follow the same set of commands.




>-   Start container:
>      -   `astro dev start`
>-   Stop container:
>       -   `astro dev stop`
>-   Check linter (Flake8, PyDocStyle y MyPY):
>       -   `./development/tools/build.sh`
>       -   `./development/tools/linter.sh`
>-   Check scheduler logs:
>       - 	`docker exec -it <container_id> /bin/bash`

Maybe you can think that it is a few commands however, in my daily work I came across the following scenarios:
>-  Misspelling commands.
>-  Forget the order of the statements.
>-  Forget to give it a start to the container when I gave it a stop.
>-  Do the linter command without doing the container build

Of course, that situation was not every day. However, I was frequently stressed because I was wasting time. Talking with my teammates I realized
that they face the same problems. That is when this situation takes relevant because is the time of an entire team wasted and is not taken advantage of to
generate value.
### Makefile
To tackle this problem, I create a Makefile with the following command:
```shell
$ touch Makefile
```
Then I added the bash commands that I used the most. I share with you my current Makefile.
#### Current Makefile
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

Now if I run the following command:
```shell
$ make help
```
it returns:
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

 ### Conclusion
So far, I've been using the previous implementation for about a month. I haven't made a mistake.  Another great thing is that my teammates have adapted and added their commands.