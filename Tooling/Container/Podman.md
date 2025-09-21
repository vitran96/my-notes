[[Container]] tool.
No daemon like [[Docker]].

# Installation

Link: https://podman.io/docs/installation

# Rootless Network

Most Docker compose file would not work on Linux if run on rootless because podman cannot modify HOST / NETWORK file.

https://containers.github.io/podman.io_old/old/community/meeting/notes/2021-10-05/Podman-Rootless-Networking.pdf
https://www.baeldung.com/linux/rootless-podman-communication-containers

Workaround:
Remove ALL `network` in the compose and use this:
```
...
services:
  service-name:
    ...
    network_mode: host
    ...
```