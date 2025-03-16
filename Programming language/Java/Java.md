A [[Programming language]] with [[Object oriented paradigm]]

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