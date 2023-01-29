---
title: "Batch Machine Learning Model Deployment"
summary: Batch ML Model with Fast API and Typer.
date: 2023-01-03
weight: 8
aliases: ["/papermod-features"]
tags: ["MLOps"]
author: "Joaquin C"
draft: false
cover:
    image: "/posts/batch_mlops_pipeline.jpg" # image path/url
    alt: "data pipeline" # alt text
    caption: "data pipeline" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

### Introduction
In some situations, it is necessary to make predictions on sample batches. Often this type of requirement is accompanied by the following specification:

- Execution must be frequent on a recurring schedule.

In this post, we'll see how we can transform that into a pipeline that works in production.

---

### The problem
To be clear, let's imagine a scenario where the data science team builds a scoring model. Stakeholders say they need to score our new users every week, our task is to put it into production.