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

# Primitive data caching

## Integer cache
`Integer` object type has caching from `-128` to `127`

```java
Integer a = 1000;
Integer b = 1000;
System.out.println(a == b); // false
System.out.println(((int) a) == ((int) b)); // true
```

Like the example above, if the number type is object type `Integer` and is out side of the range, only `equals()` will work. Otherwise, Java will do object reference compare.
This can be configured with the option below:
```shell
java -Djava.lang.Integer.IntegerCache.high=<value>
```

## Other type with cache
|Wrapper|Cached range|Notes|
|---|---|---|
|`Byte`|`-128` to `127`|Entire byte range cached.|
|`Short`|`-128` to `127`|Same default range as `Integer`.|
|`Integer`|`-128` to `127` (default)|Upper bound can be increased with `-Djava.lang.Integer.IntegerCache.high=<n>`.|
|`Long`|`-128` to `127`|Same default range as `Integer`.|
|`Character`|`\u0000` (0) to `\u007F` (127)|ASCII (0–127) characters cached.|
|`Boolean`|`false`, `true`|Both `Boolean` instances are cached.|

## What about wrapper that is not cached?
It will do object reference comparison -> always false if different object.

# Code formatting
We can use [[Maven]] plugin: `Spotless` for [[Code-linting]] and [[Code-formatting]].
[Guide](https://www.baeldung.com/java-maven-spotless-plugin)
```xml
<plugin>
    <groupId>com.diffplug.spotless</groupId>
    <artifactId>spotless-maven-plugin</artifactId>
    <version>2.43.0</version>
    <configuration>
        <java>
            <googleJavaFormat/>
        </java>
    </configuration>
</plugin>
```

Command:
```shell
# Linting
mvn spotless:check

# Foramtting
mvn spotless:apply
```

# Error prone
Static analysis tool to check for common code mistake.
Can only be used as part of compiler.
[[Maven]] config:
```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.11.0</version>
        <configuration>
          <source>17</source>
          <target>8</target>
          <encoding>UTF-8</encoding>
          <compilerArgs>
            <arg>-XDcompilePolicy=simple</arg>
            <arg>--should-stop=ifError=FLOW</arg>
            <arg>-Xplugin:ErrorProne</arg>
          </compilerArgs>
          <annotationProcessorPaths>
            <path>
              <groupId>com.google.errorprone</groupId>
              <artifactId>error_prone_core</artifactId>
              <version>${error-prone.version}</version>
            </path>
            <!-- Other annotation processors go here.

            If 'annotationProcessorPaths' is set, processors will no longer be
            discovered on the regular -classpath; see also 'Using Error Prone
            together with other annotation processors' below. -->
          </annotationProcessorPaths>
        </configuration>
      </plugin>
    </plugins>
  </build>
```