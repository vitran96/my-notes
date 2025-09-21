Docker is a [[VM]] technology but lighter compare to [[VM]] like [[virtual box]].
To start a Docker, we create an image and start a container base on it.

# Installation

```shell
sudo nala install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl status docker

docker --version

sudo usermod -aG docker $USER
newgrp docker
```