[[Java]] testing library for [[Integration Test]]

Notes:
- Although it is possible to test at service level. It is generally better to test full integration (API -> DB).
	- When is it worth to test at service level? When the SQL is complex
- Should we mix between global and class-level container?
	- Not suggested by [[Gemini AI]]

# Setup

## [[Docker]]

If [[Docker]], no need to do additional setup.

## [[Podman]]

Article: https://www.baeldung.com/java-podman-configure-testcontainers

If [[Podman]],  will require enable [[Docker]] compatible [[Socket]].
[[Ryuk container]] must be disabled. This mean `no auto-cleanup`. How to mitigate this? There are 2 solutions:
1. If we are using [[Spring-boot]] then just create it as a [[Spring-boot#Bean]] and [[Spring-boot]] will managed it (Test Container class has `AutoClosable` implemented and [[Spring-boot#Bean]] will look for it)
2. Or use `@TestContainers` and `@Container` provided by [[JUnit]]

```shell
# Start podman socket
systemctl --user enable --now podman.socket

# Validate
ls -la /run/user/$UID/podman/podman.sock

# Check if socker available
podman machine start
podman machine inspect --format '{{.ConnectionInfo.PodmanSocket.Path}}'

# Setup Linux
export DOCKER_HOST=unix://${XDG_RUNTIME_DIR}/podman/podman.sock
export TESTCONTAINERS_RYUK_DISABLED=true

# Setup MacOS
export DOCKER_HOST=unix://$(podman machine inspect --format '{{.ConnectionInfo.PodmanSocket.Path}}')
export TESTCONTAINERS_RYUK_DISABLED=true
```

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
