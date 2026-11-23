---
para: archive
was: resource
---
A [[Java]] type-safe SQL DSL / query builder (not a full [[ORM]]) for [[Relational database|SQL DB]]

# Dynamic table name

https://www.jooq.org/doc/latest/manual/sql-building/names

```java
String parameter = "entityType";
Table<?> table = table(name(parameter + "_other_stuff"));
```

# Dynamic SQL template

Possible via plain SQL templating: DSL.sql("... {0}", arg), DSL.field(...), ctx.resultQuery(...); otherwise pass fields dynamically:
Eg:
```java
ResultQuery<?> query(
	String bizDate, 
	Field<?> sortField, 
	SortOrder sortOrder
) {
	return context.selectFrom("table1")
		.where(field("report_date").eq(bizDate))
		.orderBy(sortField.sort(sortOrder));
}
```