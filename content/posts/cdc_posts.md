---
title: "Change Data Capture"
summary: Change Data Capture using Merge
date: 2022-04-30
# weight: 2
aliases: ["/papermod-features"]
tags: ["Data Engineering"]
author: "Joaquin C"
draft: false
cover:
    image: "/posts/data_pipeline.png" # image path/url
    alt: "data pipeline" # alt text
    caption: "data pipeline" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

### Introduction
Frequently I'm requested to send data in two specific situations:
- When new data arrives.
- When the data changes in the raw tables.

The stakeholders only gave us the raw tables and the specific layout. ¿How do we do that?

---

### The problem
In order to be more clear, we imagine a dummy scenario where we need to send data related to products.

#### DATA SOURCES 
In this area, we need to identify the raw tables and the relationship between them
- CATEGORY_RAW
- PRICE_RAW

the tables are related by the **sku** column.

##### CREATE TABLES
```shell
CREATE TABLE BD.SALES.CATEGORY_RAW (
    id INT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    sku VARCHAR(255) NOT NULL
);

CREATE TABLE BD.SALES.PRICE_RAW (
    id INT PRIMARY KEY,
    sku VARCHAR(255) NOT NULL,
    amount VARCHAR(255) NOT NULL
);
```

##### POPULATE TABLES

```shell
INSERT INTO BD.SALES.CATEGORY_RAW(id, name, sku)
VALUES
    (1,'Children Bicycles',  'CHI_BIC_25'),
    (2,'Comfort Bicycles',   'COM_BIC_33'),
    (3,'Cruisers Bicycles',  'CRU_BIC_25'),
    (4,'Cyclocross Bicycles','CYC_BIC_25');
    
INSERT INTO BD.SALES.PRICE_RAW(id, sku, amount)
VALUES
    (1,'CHI_BIC_25', 15000),
    (2,'COM_BIC_33', 25000),
    (3,'CRU_BIC_25', 13000),
    (4,'CYC_BIC_25', 10000);
```
---
#### STAGING AREA
This area is where we join the raw sources, which normally it's a big table. 
```shell
CREATE OR REPLACE TRANSIENT TABLE BD.SALES.REPORT_STG COMMENT = 'STG table with raw data' AS (
    SELECT
        CR.ID    
        ,CR.NAME
        ,PR.AMOUNT
    FROM
        BD.SALES.CATEGORY_RAW CR
    INNER
        JOIN BD.SALES.PRICE_RAW PR
    ON
        CR.SKU = PR.SKU
);
```
---

#### HISTORICAL TABLE
We need to define the layout of the final table because in this table we will be inserting the result of the merge strategy.
```shell
CREATE TABLE IF NOT EXISTS BD.SALES.REPORT_TARGET  COMMENT = 'Table with product data to send' (
    ACTION           TEXT                   COMMENT 'Action to do with this register CREATE, UPDATE'   
    ,CATEGORY_ID     INT PRIMARY KEY        COMMENT 'Unique identifier'
    ,CATEGORY_NAME   VARCHAR(255) NOT NULL  COMMENT 'Category name'
    ,AMOUNT          DECIMAL(10,2)          COMMENT 'Price'
    ,CREATED_AT      DATE                   COMMENT 'Date when this register was inserted or updated'
    ,STATE           TEXT                   COMMENT 'Register state, SEND, SENDED'
);
```
---

#### POPULATE
In this moment is when we apply the merge strategy 
```shell
MERGE INTO BD.SALES.REPORT_TARGET T 
    USING BD.SALES.REPORT_STG S
ON 
    S.ID = T.CATEGORY_ID

WHEN NOT MATCHED 
    THEN INSERT 
    (
        T.ACTION
        ,T.CATEGORY_ID
        ,T.CATEGORY_NAME
        ,T.AMOUNT
        ,T.STATE
        ,T.CREATED_AT
    )
    VALUES 
    (
        'CREATE'
        ,S.ID
        ,S.NAME
        ,S.AMOUNT
        ,'SEND'
        ,CURRENT_DATE()
    )

WHEN MATCHED AND T.STATE = 'SEND' THEN UPDATE SET 
    T.STATE  = 'SENDED'

WHEN MATCHED AND 
    (
        T.CATEGORY_NAME <> S.NAME
            OR  
        T.AMOUNT <> S.AMOUNT   
    )
    THEN 
        UPDATE SET 
            ACTION = 'UPDATE'
            ,T.CATEGORY_NAME = S.NAME
            ,T.AMOUNT = S.AMOUNT
            ,T.STATE  = 'SEND'
            ,T.CREATED_AT = CURRENT_DATE()
;
```
---

#### SEND DATA
Normally I work with Snowflake DWH and in this particular example we send the data in json lines to S3, to achived that I create a stage in snowflake.
```shell
COPY INTO @STAGE_NAME/products/products_lists FROM (
    SELECT
        '{' ||  '"action"' || ':' ||  '"' ||  "ACTION" || '"' || ',' ||
                '"id"' || ':' ||  '"' || "CATEGORY_ID" || '"' || ',' ||
                '"name"' || ':' || '"' || "CATEGORY_NAME" || '"'  || ',' || 
                '"amount"' || ':'  || '"' || "AMOUNT"  || '"' || 
        '}' AS "category_list"
    FROM
         BD.SALES.REPORT_TARGET
    WHERE
        1=1
        AND STATE = 'SEND'
)
```
