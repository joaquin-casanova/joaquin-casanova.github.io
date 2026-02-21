---
title: "Data Architecture"
summary: Basic Data Architecture
date: 2022-06-08
# weight: 4
aliases: ["/posts/data_architecture_posts/"]
tags: ["Data Engineering"]
author: "Joaquin C"
draft: false
cover:
    image: "/posts/data-engineering/images/bridge.jpeg" # image path/url
    alt: "data architecture" # alt text
    caption: "data architecture" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

### Disclaimer
This post is WIP but I would to share you.

### Introduction
One of the DATA ENGINEERING TEAM's main objectives is to ensure data reliability and scalability; to achieve that, the infrastructure plays a main role.

---

### Data Architecture
The DevOps and SWE best practices play a fundamental role, we try to implement both in DE.

So far I used at least one year the following architecture:
![architecture image](/posts/data-engineering/images/data_architecture.png)
The idea behind is focuses in:

> - TDD
> - Quick deploy
> - Decoupling SQL scripts
> - Continous Deployment and Integration
> - Production and Development enviroments

### How works?
1. We work following the [Gitflow Workflow](https://www.atlassian.com//git/tutorials/comparing-workflows/gitflow-workflow), which allows us to work on the differents requirements at the same time.

2. Every time somebody does a PR automatically runs the following tests (These tests also can run local):
    - Flake8
    - PyDocStyle
    - MyPY

3. We need somebody in the team to check our PR to merge in the development instance.

4. Finally it all works ok, we make a PR from DEV to MASTER. (The master branch only accepts PR from the DEV branch. )