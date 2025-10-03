A [[Relational database]] 
Really popular
I have used this with [[emotive]]‘s [[OTX SU]] project

# MD5 encrypt on column(s)
[[MD5]] encryption
Below code will encrypt a column when use with [[SQL]] statement. However, if a column is NULL then the hash is NULL
```sql
md5(accountid || accounttype || createdby || editedby);
```

A alternative solution is to convert to [[JSON]] then [[hash]] it. This will however, depends on input order. Changing order will give different result
```sql
SELECT md5(ROW(col1, col2, col3)::TEXT)
FROM mytable
```

# Array

## Array column

```sql
CREATE TABLE :table_name (
	col1 VARCHAR(100)[] DEFAULT ARRAY[]::VARCHAR[];
```

# Generated column

```sql
CREATE TABLE :table_name (
	col1 GENERATED { ALWAYS | BY DEFAULT } AS IDENTITY
)
```

