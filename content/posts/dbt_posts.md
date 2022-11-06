---
title: "Data build tool"
summary: How run dbt on production
date: 2022-11-03
weight: 5
aliases: ["/papermod-features"]
tags: ["Data Engineering"]
author: "Joaquin C"
draft: false
cover:
    image: "/posts/analytics-engineering-dbt.png" # image path/url
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
dbt Cloud is the fastest and most reliable way to deploy dbt but also comes with a [price](https://www.getdbt.com/pricing/) if you don't want be a unique use.

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
![dbt version image](/posts/dbt_version.png#center)

### Start a dbt project
At this time, you had all you need to start a dbt project, to do that run the following command:
```shell
 $ dbt init
```
If this is your first time ever using the tool, it will:

> - Ask you to name your project.
> - Ask you which database adapter you're using (or to Supported Data Platforms).
> - prompt you for each piece of information that dbt needs to connect to that database: account,user, password, etc.

![dbt init image](/posts/dbt_init.png#center)

You will get a folder with the name of your project with the following structure:
```
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

Then you can use docker to run dbt, as you will read in this link  [docker for dbt](https://github.com/dbt-labs/dbt-core/tree/main/docker). This docker file is suitable for building dbt Docker images locally or using with CI/CD to automate populating a container registry.

