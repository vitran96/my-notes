---
para: resource
tags: [homelab]
---
Open-source FreeBSD-based firewall/router distribution. See also: [[Proxmox]], [[Tailscale]]

# Install packages

System > Package Manager; settings persist in `/cf/conf/config.xml` (restore/backup). Either via `config.xml` or UI.

# [[pfBlockerNG]]

Require fetching blocker list from [[GitHub]]

# [[WireGuard]]

Can easily installed

# [[DNS Resolver]]

`DNS Resolver` = Unbound. To use as [[DNS Resolver]], it must be configured
- Update [[DNS]] rule
- Update [[ACL]] rule