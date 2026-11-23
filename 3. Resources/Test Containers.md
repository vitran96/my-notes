---
para: resource
tags: [java, testing]
---
[[Java]] testing library for [[Integration Test]]

Notes:
- Although it is possible to test at service level. It is generally better to test full integration (API -> DB).
	- When is it worth to test at service level? When the SQL is complex
- Should we mix between global and class-level container?
	- Avoid mixing: it is easy to start duplicate containers

# Setup

## [[Docker]]

If [[Docker]], no need to do additional setup.

## [[Podman]]

Article: 
- https://www.baeldung.com/java-podman-configure-testcontainers
- https://podman-desktop.io/tutorial/testcontainers-with-podman

If [[Podman]],  will require enable [[Docker]] compatible [[Socket]].
[[Ryuk container]] may fail on rootless Podman: disable it (`TESTCONTAINERS_RYUK_DISABLED=true`) or allow privileged (`TESTCONTAINERS_RYUK_CONTAINER_PRIVILEGED=true`). Disabling it means `no auto-cleanup`. How to mitigate this? There are 2 solutions:
1. If we are using [[Spring-boot]] then just create it as a [[Spring-boot#Bean]] and [[Spring-boot]] will managed it (Test Container class has `AutoClosable` implemented and [[Spring-boot#Bean]] will look for it)
2. Or use `@Testcontainers` and `@Container` (from Testcontainers' `junit-jupiter` module, not JUnit)

```shell
# Start podman socket
systemctl --user enable --now podman.socket

# Validate
ls -la /run/user/$UID/podman/podman.sock

# Setup Linux
export DOCKER_HOST=unix://${XDG_RUNTIME_DIR}/podman/podman.sock
export TESTCONTAINERS_RYUK_DISABLED=true

# Setup MacOS
# Check if socket available
podman machine start
podman machine inspect --format '{{.ConnectionInfo.PodmanSocket.Path}}'
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

# [[MySQL]] container

Contain default `mysql.cnf` that can cause error on latest [[MySQL]] version.

## Override `cnf` file

%% TODO: %%

See also: [[Maven failsafe]], [[Java testing]]
