---
title: "read-only group Amazon Redshift"
summary: quick grant select?
date: 2023-04-20
# weight: 6
aliases: ["/posts/redshift_read-only/"]
tags: ["Data Engineering"]
author: "Joaquin C"
draft: false
cover:
    image: "/posts/data-engineering/images/read_sql.jpg" # image path/url
    alt: "read database" # alt text
    caption: "library database" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

### Introduction
Data has become an essential part of every business and everyone wants to be a data-driven company. However, due to its volume, variety and velocity, it is necessary to find ways to organise the information in a modern solution. That's why a key element is a data warehouse.

As a data engineer, sometimes you may need to provide access to the DWH, but how do you manage that?

### The stakeholders
As Data Enginners, we often receive requests from different stakeholders: finance, marketing, sales, etc. 

### The requests
To handled this example a stakeholder requests access to specific table in a specific schema.


### Get started
>- Create a read only group
```shell
CREATE GROUP ro_group;
```

>- Create user
```shell
CREATE USER "read_user" PASSWORD 'PASSWORD';
```

>- Add user to read only group
```shell
ALTER GROUP ro_group ADD USER "read_user";
```

>- Grant usage permission to read only group to specific schema
```shell
GRANT USAGE ON SCHEMA "schema_name" TO GROUP ro_group;
```

>- Grant select permission to read only group to specific schema
```shell
GRANT SELECT ON ALL TABLES IN SCHEMA "schema_name" TO GROUP ro_group;
```

>- Alter default privileges to maintain the permission on new tables
```shell
ALTER DEFAULT PRIVILEGES IN SCHEMA "schema_name" GRANT SELECT ON TABLES TO GROUP ro_group;
```
At this point you have a group with read-only permissions and a user who belongs to that group.

### More general
A few days ago I was asked to create a new group and grant select to all tables in all schemas, I was looking for a function to handle that but I couldn't find it so I created a procedure.

At this point you need to have a group and a user added to the group, but you know how to do that.

The first thing to do is to find a way to generate all the grants for all the schemas. 

>- Query to get the syntax to grant a read-only group permission to a specific schema.
```shell
--Note: The following query only returns the syntax but does not make any changes to the base.

SELECT 'GRANT USAGE ON SCHEMA ' || schema_name || ' TO GROUP ro_group;'
FROM information_schema.schemata
WHERE schema_name NOT LIKE 'pg_%' AND schema_name <> 'information_schema'
```

To apply the grant usage on all schemas I created the following procedure:

```shell
CREATE OR REPLACE procedure grant_usage_all_schemas_to_group()
LANGUAGE plpgsql
AS $$
DECLARE
  schema_record record;
BEGIN
  FOR schema_record in
  		SELECT schema_name
  		FROM information_schema.schemata
  		WHERE schema_name NOT LIKE 'pg_%' AND schema_name <> 'information_schema'
  LOOP
    EXECUTE 'GRANT USAGE ON SCHEMA ' || schema_record.schema_name || ' TO GROUP ro_group;';
  END LOOP;
END;
$$ ;
```
and to apply I ran the following:
```shell
call grant_usage_all_schemas_to_group();
```
Now you need to Grant select permission to read only group to all schemas

>- Grant select permission to read only group to specific schema
```shell
--Note: The following query only returns the syntax but does not make any changes to the base.

SELECT 'GRANT SELECT ON ALL TABLES IN SCHEMA ' || schema_name || ' TO GROUP RO_GROUP;'
FROM information_schema.schemata
WHERE schema_name NOT LIKE 'pg_%' AND schema_name <> 'information_schema'
```
To apply the grant select on all schemas I created the following procedure:

```shell
CREATE OR REPLACE procedure grant_select_all_tables_to_group()
LANGUAGE plpgsql
AS $$
DECLARE
  schema_record record;
BEGIN
  FOR schema_record in
  		SELECT schema_name
  		FROM information_schema.schemata
  		WHERE schema_name NOT LIKE 'pg_%' AND schema_name <> 'information_schema'
  LOOP
    EXECUTE 'GRANT SELECT ON ALL TABLES IN SCHEMA ' || schema_record.schema_name || ' TO GROUP ro_group;';
  END LOOP;
END;
$$ ;

```
To apply the grant select on all tables I created the following procedure:
```shell
call grant_select_all_tables_to_group();
```

>- Alter default privileges to maintain the permission on new tables

```shell
--Note: The following query only returns the syntax but does not make any changes to the base.

SELECT 'ALTER DEFAULT PRIVILEGES IN SCHEMA ' || schema_name || ' GRANT SELECT ON TABLES TO GROUP ro_group;'
FROM information_schema.schemata
WHERE schema_name not like 'pg_%' and schema_name <> 'information_schema'
```

```shell
CREATE OR REPLACE procedure grant_alter__default_all_tables_to_group()
LANGUAGE plpgsql
AS $$
DECLARE
  schema_record record;
BEGIN
  FOR schema_record in
  		SELECT schema_name
  		FROM information_schema.schemata
  		WHERE schema_name NOT LIKE 'pg_%' AND schema_name <> 'information_schema'
  LOOP
    EXECUTE 'ALTER DEFAULT PRIVILEGES IN SCHEMA ' || schema_record.schema_name || ' GRANT SELECT ON TABLES TO GROUP ro_group;';
  END LOOP;
END;
$$ ;
```
```shell
call grant_alter__default_all_tables_to_group()
```

### Final Thoughts
At this point you have a group with read only permissions and you only need added users to grant the select, I think it is possible to be more DRY and use a single procedure to do the last 3...maybe in a few days I will do an update.

For now this works for me.