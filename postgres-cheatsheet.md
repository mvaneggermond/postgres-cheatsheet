## Strings

```sql

SELECT 	
	CASE WHEN bar ~ '^[0-9]' THEN do_something
    ELSE do_something_else END 
FROM foo_tbl

```


## Date time

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