[[VPN Mesh]] service

# Exit node

This will change the [[VPN client]] whole [[Network]] to the target network config, as if I am in that network.
Exit node require approval in Dashboard.

# Setup [[pfsense]] with [[Virtual subnet]]

Ideally, his is good but this doesn't work for me.

# Install in [[Termux]]

Install via [[tailscaled]]

```shell
# Install curl and net-tools if missing
pkg update -y && pkg install curl net-tools -y

# Download and install Tailscale CLI for Termux
curl -fsSL https://raw.githubusercontent.com/bropines/tailscale-termux-cli/main/remote-install.sh | bash

# Default tailscaled work fine but must include --socket like below because it use different socket file path
# To stop, run
sv stop tailscaled
sv status tailscaled

# Authenticate and bring up Tailscale
tailscale --socket="$HOME/.tailscale/tailscaled.sock" up

# Server as https within tailnet (must enable in Admin > DNS)
tailscale --socket="$HOME/.tailscale/tailscaled.sock" serve --bg http://127.0.0.1:11434

# To test with curl
curl --socks5-hostname localhost:1055 https://phone-2-llm.tail78e763.ts.net/v1/models
```

# Log

- 2026-08-04 - I give up on using 1 node to expose my infra. The likely best practice way is to have my router using non-common [[LAN]] IP (like 192.168.58.0) instead of (192.168.1.0). [[WireGuard]] would not help either since [[iOS]] doesn't allow nested [[VPN]] connection
- 2026-08-17 - For some reason it stop working. Reinstall [[tailscaled]] make it work