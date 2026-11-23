---
para: archive
was: resource
---
Docker is a [[Container]] platform. Unlike a [[VM]] (e.g. [[Virtualbox|VirtualBox]]), a container shares the host kernel and is isolated with Linux namespaces and cgroups, so it is much lighter. Build an image (from a Dockerfile), then run containers from it.

# Installation

Requires Docker's apt repository first (docs.docker.com/engine/install).

```shell
sudo nala install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl status docker

docker --version

sudo usermod -aG docker $USER
newgrp docker
```