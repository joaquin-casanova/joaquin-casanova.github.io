---
title: "Change Data Capture"
summary: Change Data Capture using Merge
date: 2022-03-24
weight: 2
aliases: ["/papermod-features"]
tags: ["Data Engineering"]
author: "Joaquin C"
draft: true
cover:
    image: "/posts/data_pipeline.png" # image path/url
    alt: "data pipeline" # alt text
    caption: "data pipeline" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

### Introduction
Frequently I'm requested to send data in two specific situations:
- When arrived new data.
- When the data changes in the raw tables.

The stakeholders only gave us the raw tables and the specific layout. ¿How do we do that?

---

### Strategy

