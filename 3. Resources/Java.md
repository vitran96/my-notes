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
Premain-Class="package.to.TestAgent"
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

# String pool

# Java version update

I will take in LTS change-log only

## Java 25

## Java 21

## Java 17

## Java 8