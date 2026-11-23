---
para: resource
tags: [java]
---
A [[Programming language]] with [[Object oriented paradigm]]

# JDK distributions

These are JDK distributions (see [[Backend interview questions#JDK, JRE, JVM]] for JDK vs JRE vs JVM):
- [[OpenJDK]]
- [[OracleJDK]]
- [[Eclipse Temurin]]
- [[Amazon Corretto]]
- [[Microsoft Build of OpenJDK]]
- [[Oracle GraalVM]]
- [[JetBrains Runtime]]
- [[Azul Zulu]]

Most are builds of OpenJDK.

# Java agent Premain-class
Getting started with Java's instrument agent

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
```
Premain-Class: package.to.TestAgent
```

Run with:
```shell
java -javaagent:agent.jar -jar app.jar
```

%% Note about Java special API to get incremental / debug data %%

# Install Java from cli on [[Window OS]]

Silent install:

```powershell
.\jdk.exe /s
```

Eg:

```powershell
.\jdk-8u221-windows-i586.exe /s
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

`==` on wrappers always compares references. Autoboxing uses `Integer.valueOf`, which returns cached objects for -128..127, so `==` happens to be true there and false outside. Always use `equals()`. Also `-XX:AutoBoxCacheMax=<n>`.
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
`Float`/`Double` (no cache): `valueOf` returns a new object.

# String pool

```java
public class StringPoolExample {
    public static void main(String[] args) {
        // Created in the String Pool
        String s1 = "Java";
        String s2 = "Java";

        // Created in the Heap (Outside the Pool)
        String s3 = new String("Java");

        // Comparison using == (Checks memory address)
        System.out.println(s1 == s2); // true -> Both point to the same object in the pool
        System.out.println(s1 == s3); // false -> s3 is a different object in the heap

        // Comparison using .equals() (Checks actual text content)
        System.out.println(s1.equals(s3)); // true -> The content is the same
        
        // String literal concatenation save in String pool
        String s5 = "Java" + "Programming"; 
		String s6 = "JavaProgramming";

		System.out.println(s5 == s6); // true
    }
}
```

## intern()

```java
String s1 = "Java";
String s3 = new String("Java");
String s4 = s3.intern(); // Fetch the reference from the pool

System.out.println(s1 == s4); // true
```

# JVM profiling

%% TODO: %%

# JVM Instrument

%% TODO: %%

# Time module

`JavaTimeModule` is an important module to serialize/deserialize date & time.
The module follow JSR-310.
`JavaTimeModule` is Jackson's (`jackson-datatype-jsr310`), not the JDK; Spring Boot registers it automatically. %% verify: Jackson 3 builds java.time support in %%

# Virtual thread

Since Java 21 (JEP 444). Many virtual threads are scheduled M:N onto a few carrier platform threads; a blocking IO call unmounts the virtual thread. Great for IO-bound, thread-per-request code; no gain for CPU-bound work (use a sized platform pool). Pinning inside `synchronized` was fixed in Java 24 (JEP 491).

# File API

## java.nio.file.Path/Files vs java.io.File

%% TODO: %%

# Stream API

## Does stream pipeline run right away?

No. Intermediate ops (`map`, `filter`, `peek`) are lazy and run only when a terminal op (`collect`, `forEach`, `count`) is called, element by element.

# Thread API

## Is parallel better than executor?

%% TODO: %%

# Java version update

I will take in LTS change-log only

## [[Java 25]]

## [[Java 21]]

## [[Java 17]]

## [[Java 8]]