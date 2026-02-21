---
title: "Data build tool"
summary: How run dbt on production
date: 2022-11-03
# weight: 5
aliases: ["/posts/dbt_posts/"]
tags: ["Data Engineering"]
author: "Joaquin C"
draft: false
cover:
    image: "/posts/data-engineering/images/analytics-engineering-dbt.png" # image path/url
    alt: "data pipeline" # alt text
    caption: "data pipeline" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

### Introduction
dbt is a transformation workflow that helps you get more work done while producing higher quality results. You can use dbt to modularize and centralize your analytics code, while also providing your data team with guardrails typically found in software engineering workflows. Collaborate on data models, version them, and test and document your queries before safely deploying them to production, with monitoring and visibility.

dbt compiles and runs your analytics code against your data platform, enabling you and your team to collaborate on a single source of truth for metrics, insights, and business definitions. This single source of truth, combined with the ability to define tests for your data, reduces errors when logic changes, and alerts you when issues arise.

As dbt user, your main focus will be on writing models (i.e select queries) that reflect core business logic.

---

### Get started with dbt
The first thing that you need to know is that dbt can be access through **dbt Core** or **dbt Cloud**.
> - #### dbt Cloud
dbt Cloud is the fastest and most reliable way to deploy dbt but also comes with a [price](https://www.getdbt.com/pricing/) if you don't want to be an unique user.

> - #### dbt Core
dbt Core is an open-source tool, that we can use to deploy dbt without cost. (Spoiler alert: We'll choose this way)

### Install dbt Core
According to the [dbt Core Installation](https://docs.getdbt.com/docs/get-started/installation), the recommended for macOS is to use [Homebrew](https://brew.sh/) to install dbt.

Then we can use it to install dbt by running the following commands:
```shell
$ brew update
$ brew install git
$ brew tap dbt-labs/dbt
```
Now you're ready to install dbt, I will install a redshift adapter but you can choose whatever you want. Check the list 
here: [dbt-adapters](https://docs.getdbt.com/docs/supported-data-platforms)
```shell
$ brew install dbt-redshift
```
To check if you installed dbt-core successfully run the following command:
```shell
$ dbt --version
```
Should you get something quite similar to the following image
![dbt version image](/posts/data-engineering/images/dbt_version.png#center)

### Start a dbt project
At this time, you had all you need to start a dbt project, to do that run the following command:
```shell
$ dbt init
```
If this is your first time ever using the tool, it will:

> - Ask you to name your project.
> - Ask you which database adapter you're using (or to Supported Data Platforms).
> - prompt you for each piece of information that dbt needs to connect to that database: account,user, password, etc.

![dbt init image](/posts/data-engineering/images/dbt_init.png#center)

You will get a folder with the name of your project with the following structure:
``` shell 
├── README.md
├── analyses
├── dbt_project.yml
├── macros
├── models
│   └── example
│       ├── my_first_dbt_model.sql
│       ├── my_second_dbt_model.sql
│       └── schema.yml
├── seeds
├── snapshots
└── tests
```
At this point you have a dbt project locally and can start to test and build models. However, the idea is to try to collaborate with a team.

### Docker for dbt
You can use docker to run dbt, as you will read in this link  [docker for dbt](https://github.com/dbt-labs/dbt-core/tree/main/docker).

This docker file is suitable for building dbt Docker images locally or using CI/CD to automate populating a container registry.

#### Dockerfile
I share you my current Dockerfile.

```shell
# pull official base image
FROM python:3.10.6-slim-bullseye as base

#install system dependencies
RUN apt-get update \
  && apt-get dist-upgrade -y \
  && apt-get install -y --no-install-recommends \
    git \
    ssh-client \
    software-properties-common \
    make \
    build-essential \
    ca-certificates \
    libpq-dev \
  && apt-get clean \
  && rm -rf \
    /var/lib/apt/lists/* \
    /tmp/* \
    /var/tmp/*

# set enviroments variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1
ENV PYTHONIOENCODING=utf-8
ENV LANG=C.UTF-8
ENV DBT_PROFILES_DIR /usr/src/app


# update python
RUN python -m pip install --upgrade pip setuptools wheel --no-cache-dir

# set working directory
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

#add app

COPY . .

ENTRYPOINT ["dbt"]


##
# dbt-core
##
FROM base as dbt-core
ARG dbt_core_ref=dbt-core@v1.3.0

RUN python -m pip install --no-cache-dir "git+https://github.com/dbt-labs/${dbt_core_ref}#egg=dbt-core&subdirectory=core"


##
# dbt-redshift
##
FROM base as dbt-redshift
ARG dbt_redshift_ref=dbt-redshift@v1.3.0

RUN python -m pip install --no-cache-dir "git+https://github.com/dbt-labs/${dbt_redshift_ref}#egg=dbt-redshift"


##
# dbt-third-party
##
FROM dbt-core as dbt-third-party
RUN python -m pip install --no-cache-dir "${dbt_third_party}"

# pull dbt dependencies
RUN dbt deps

# build static pages
RUN dbt docs generate
```
This Dockerfile must be in the same folder that the project
![dbt docker image](/posts/data-engineering/images/dbt_docker.png#center)


#### Build the image
```shell
$ docker build --tag data-models  --target dbt-redshift .
```

#### Run the image
```shell
$ docker run -e DB_NAME='' -e DB_HOST='' -e DB_PASSWORD='' -e DB_PORT='' -e DBT_USER='' -t data-models run 
```


#### Run the docs
```shell
$ docker run -p 8001:8001 -e DB_NAME='' -e DB_HOST='' -e DB_PASSWORD='' -e DB_PORT='' -e DBT_USER='' -t data-models docs serve --port 8001
```