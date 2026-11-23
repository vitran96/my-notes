---
para: resource
tags: [database, sql]
---
A [[Relational database]] 
Really popular

# MD5 hash on column(s)
[[MD5]] hash
Below code will hash a column when use with [[SQL]] statement. However, if a column is NULL then the hash is NULL
```sql
md5(accountid || accounttype || createdby || editedby);
```

An alternative solution is to hash the row: `ROW(...)::text` is the row's text form; NULLs become empty, so it survives NULLs; result depends on column order.
```sql
SELECT md5(ROW(col1, col2, col3)::TEXT)
FROM mytable
```

# Array

## Array column

```sql
CREATE TABLE t (col1 VARCHAR(100)[] DEFAULT ARRAY[]::VARCHAR[]);
```

# Identity column

```sql
CREATE TABLE :table_name (
    id INT GENERATED { ALWAYS | BY DEFAULT } AS IDENTITY
)
```

# Generated column

```sql
CREATE TABLE :table_name (
    price NUMERIC,
    qty INT,
    total NUMERIC GENERATED ALWAYS AS (price * qty) STORED
)
```

PG 18 also has `VIRTUAL`, its default.

See also: [[JPA]], [[Flyway]], [[Liquibase]]
