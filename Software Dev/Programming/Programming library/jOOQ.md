A [[Java]] ‘s [[ORM]] library to interact with [[SQL]] [[database]]
# Dynamic table name

https://www.jooq.org/doc/latest/manual/sql-building/names

```java
String parameter = "entityType";
Table<?> table = table(name(parameter + "_other_stuff"));
```

# Dynamic SQL template
Not possible
Workaround is by dynamically pass in fields
Eg:
```java
RequestQuery<?> query(
	String bizDate, 
	Field<?> sortField, 
	SortOrder sortOrder
) {
	return context.selectFrom("table1")
		.where(field("report_date").eq(bizDate))
		.orderBy(sortField.sort(sortOrder));
}
```