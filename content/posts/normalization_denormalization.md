---
title: "Normalization & De-Normalization"
summary: Why is important?
date: 2023-01-10
weight: 6
aliases: ["/papermod-features"]
tags: ["Data Engineering"]
author: "Joaquin C"
draft: false
cover:
    image: "/posts/database.jpg" # image path/url
    alt: "library database" # alt text
    caption: "library database" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

### Introduction
As data engineers we frequently work with databases and it is important to understand the differences between OLTP and OLAP database types.

>- **OLTP** stand for *On-Line Transaction Processing*.
>- **OLAP** stand for *On-Line Analytical Processing*.

### OLTP
The main purpose of the OLTP system is to record typical business transactions related to an organization's activities, such as

- payments (received and made)
- orders placed
- etc.

The emphasis is on *fast processing*, because OLTP databases are read, written and updated frequently. 

This type of transactional data needs to be:

>- [ACID](https://www.databricks.com/glossary/acid-transactions)
>- Highly normalized

#### Normalization
Normalization is a method of splitting large tables into smaller ones to improve data integrity and to remove data duplication.

However calculate metrics is expensive  due to large normalization it is necessary to perform many joins between tables.
### OLAP
The main propouse of the OLAP system is support complex
analysis.
#### De-Normalization
De-normalization is an optimization technique with the aim of speeding up data retrieval.