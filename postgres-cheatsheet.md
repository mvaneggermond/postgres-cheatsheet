Postgres cheatsheet
========================
## Overview
  
  * [SQL](#sql)
  * [Strings](#strings)
  * [Date and time](#date-and-time)
  * [Window functions](#window-functions)
  * [Admin stuff](#admin-stuff)
  * [Restore / Dump](#restore-dump)

## SQL

### Reciprocal relationships
Extract unique pairs from table containing reciprocal relationships

```sql
SELECT DISTINCT 
	CASE WHEN Person1 > Person2 THEN Person2 ELSE Person1 END as Person1, 
	CASE WHEN Person1 > Person2 THEN Person1 ELSE Person2 END as Person2 
FROM couples 
WHERE relationshiptype = 'Married'
```

## Strings

```sql

SELECT 	
	CASE WHEN bar ~ '^[0-9]' THEN do_something
    ELSE do_something_else END 
FROM foo_tbl

```


## Date and time

Convert an interval into a number of hours
```sql
SELECT 
	EXTRACT(epoch FROM my_interval)/3600
FROM foo_tbl
```

Convert seconds (integer) to HH:MM:SS
```sql
SELECT 
	TO_CHAR((time_in_seconds || ‘ second’)::interval, ‘HH24:MI:SS’) AS time_in_hhmmss 
FROM foo_tbl

```


## Window functions

Create a field with a row number.

```sql
SELECT   row_number() over (ORDER BY <field> nulls last) as rownum, *
FROM     foo_tbl
ORDER BY <field>
```

## Add a sequence to an existing column

```sql
-- blocks of commands to turn foo into bar
CREATE SEQUENCE schema_name.table_seq;
ALTER TABLE schema_name.table ALTER COLUMN id SET DEFAULT nextval('schema_name.table_seq');
ALTER TABLE schema_name.table ALTER COLUMN id SET NOT NULL;
ALTER SEQUENCE schema_name.table OWNED BY schema_name.table.id;    -- 8.2 or later

SELECT MAX(id) FROM schema_name.table;
SELECT setval('schema_name.table_seq', 5);  -- replace 5 by SELECT MAX result
```sql

## Admin stuff

View the size per schema

```sql
SELECT schema_name,
    pg_size_pretty(sum(table_size)::bigint) as "disk space",
    (sum(table_size) / pg_database_size(current_database())) * 100
        as "percent"
FROM (
     SELECT pg_catalog.pg_namespace.nspname as schema_name,
         pg_relation_size(pg_catalog.pg_class.oid) as table_size
     FROM   pg_catalog.pg_class
         JOIN pg_catalog.pg_namespace
             ON relnamespace = pg_catalog.pg_namespace.oid
) t
GROUP BY schema_name
ORDER BY schema_name
```

## Restore / dump

```
psql -U your_user_name your_db_name < your_dump_file
```