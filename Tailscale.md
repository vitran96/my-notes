[[VPN Mesh]] service

# Exit node

This will change the [[VPN client]] whole [[Network]] to the target network config, as if I am in that network.
Exit node require approval in Dashboard.

# Setup [[pfsense]] with [[Virtual subnet]]

Ideally, his is good but this doesn't work for me.

# Log

- 2026-08-04 - I give up on using 1 node to expose my infra. The likely best practice way is to have my router using non-common [[LAN]] IP (like 192.168.58.0) instead of (192.168.1.0). [[WireGuard]] would not help either since [[iOS]] doesn't allow nested [[VPN]] connection