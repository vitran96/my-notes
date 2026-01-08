[[Java]] testing library for [[Integration Test]]

Notes:
- Although it is possible to test at service level. It is generally better to test full integration (API -> DB).
	- When is it worth to test at service level? When the SQL is complex

# Generic container

```java
import org.testcontainers.containers.GenericContainer;


GenericContainer<?> container = new GenericContainer<>("redis:7").withExposedPorts(6379);
container.start();
String host = container.getHost();
int hostPort = container.getMappedPort(6379);
System.out.println("Redis started on " + host + ":" + hostPort);
container.stop();
```
