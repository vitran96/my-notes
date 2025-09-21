A [[Programming language]] with [[Object oriented paradigm]]

# JRE
[[Java]] Runtime Environment

JRE has multiple implementation:
- [[OpenJDK]]
- [[OracleJDK]]
- [[AWS JDK]]
- [[MS Iron JDK]]
- [[Oracle GraalVM]]
- [[Jetbrain JVM]]

# Java agent Premain-class
[[Getting started]] with Java's instrument agent

TestAgent.java
```java
import java.lang.instrument.Instrumentation;

public class TestAgent {
	public static void premain(
		String agentArgument, 
		Instrumentation instrument
	) {
		// Instrument main class
	}
}
```

manifest.mf
```toml
Premain-Class: package.to.TestAgent
```

%% Note about Java special API to get incremental / debug data %%

# Install Java from cli on [[Window OS]]

Silent install:

```bash
.\\jdk.exe /s
```

Eg:

```bash
.\\jdk-8u221-windows-i586.exe /s
```

# jaregu queries
[[Java]] [[SQL]] templating library
Not very famous

# jOOQ

A [[Java]] ‘s [[ORM]] library to interact with [[SQL]] [[database]]
## Dynamic table name

https://www.jooq.org/doc/latest/manual/sql-building/names

```java
String parameter = "entityType";
Table<?> table = table(name(parameter + "_other_stuff"));
```

## Dynamic SQL template
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