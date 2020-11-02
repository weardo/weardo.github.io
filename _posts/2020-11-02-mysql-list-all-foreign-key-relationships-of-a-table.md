---
layout: post
title: MySQL - List all foreign key relationships referencing to a particular table?
date: 2020-11-02 19:08 +0530
---

You have a table and you want to see all the other tables which have the foreign key constraints pointing to that table, or to a particular column in that table.

**To see foreign key  relationships of a table:**
```sql
SELECT
    TABLE_NAME,
    COLUMN_NAME,
    CONSTRAINT_NAME,
    REFERENCED_TABLE_NAME,
    REFERENCED_COLUMN_NAME
FROM
    INFORMATION_SCHEMA, KEY_COLUMN_USAGE
WHERE
	REFERENCED_TABLE_SCHEMA = 'db_name'
    AND	REFERENCED_TABLE_NAME = 'table_name';
```


**To see foreign key relationships of a column:**
```sql
SELECT
    TABLE_NAME,
    COLUMN_NAME,
    CONSTRAINT_NAME,
    REFERENCED_TABLE_NAME,
    REFERENCED_COLUMN_NAME
FROM
    INFORMATION_SCHEMA.KEY_COLUMN_USAGE
WHERE
	REFERENCED_TABLE_SCHEMA = 'db_name'
    AND REFERENCED_TABLE_NAME = 'table_name'
    AND REFERENCED_COLUMN_NAME = 'column_name';
```

You might not need to address the ```REFERENCED_TABLE_SCHEMA``` for the current database you're opening.

