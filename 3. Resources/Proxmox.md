# Log

## 2026-05-23
I used Proxmox GUI installer
- Default username is root
- Type in password, email, timezone, static IP, hostname

### Network issue

If you encounter network issue and you are sure your [[Internet router]] and [[Internet switch]] and [[Ethernet]] work fine, check your [[Network]] interface

My issue was: my server is a laptop with no [[Ethernet]] so I have to use USB-2-Ethernet adapter
- Type-C hub messing with my adapter detection on the OS
- The internet didn't work and I have to enable it + reconfig the interface sine the bridge also not work
- Change DNS to router

```shell
# Command to check
ip link show
ip route show
ip adrr

lsusb


# Try connect to online resource
ping 8.8.8.8
nslookup google.com

systemctl restart networking
```

Setup the interface
``` file="/etc/networking/interface"
auto lo
ifave lo inet loopback

auto enx00000
iface enx00000 inet manual

auto vmbr0
iface vmbr0 inet static
    address 192.168.1.150/24
    gateway 192.168.1.1
    bridge-ports enx00000
    bridge-stp off
    bridge-fd 0
```

Update DNS
``` file="/etc/resolv.conf"
nameserver 192.168.1.1
```

## 2026-05-26

- [[apt]] update issue
- UI warning missing subscription
```
Linux proxmox-master 6.17.2-1-pve #1 SMP PREEMPT_DYNAMIC PMX 6.17.2-1 (2025-10-21T11:55Z) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
root@proxmox-master:~# apt update
Get:1 http://security.debian.org/debian-security trixie-security InRelease [43.4 kB]
Hit:2 http://deb.debian.org/debian trixie InRelease       
Get:3 http://deb.debian.org/debian trixie-updates InRelease [47.3 kB]
Get:4 http://security.debian.org/debian-security trixie-security/main amd64 Packages [175 kB]
Get:5 http://security.debian.org/debian-security trixie-security/main Translation-en [111 kB]
Err:6 https://enterprise.proxmox.com/debian/ceph-squid trixie InRelease
  401  Unauthorized [IP: 51.79.228.122 443]
Err:7 https://enterprise.proxmox.com/debian/pve trixie InRelease
  401  Unauthorized [IP: 51.79.228.122 443]
Error: Failed to fetch https://enterprise.proxmox.com/debian/ceph-squid/dists/trixie/InRelease  401  Unauthorized [IP: 51.79.228.122 443]
Error: The repository 'https://enterprise.proxmox.com/debian/ceph-squid trixie InRelease' is not signed.
Notice: Updating from such a repository can't be done securely, and is therefore disabled by default.
Notice: See apt-secure(8) manpage for repository creation and user configuration details.
Error: Failed to fetch https://enterprise.proxmox.com/debian/pve/dists/trixie/InRelease  401  Unauthorized [IP: 51.79.228.122 443]
Error: The repository 'https://enterprise.proxmox.com/debian/pve trixie InRelease' is not signed.
Notice: Updating from such a repository can't be done securely, and is therefore disabled by default.
Notice: See apt-secure(8) manpage for repository creation and user configuration details.
root@proxmox-master:~# ^C
root@proxmox-master:~# 
```

Fix
```shell
# Disable enterprise repose
sed -i 's/Enabled: yes/Enabled: no/' /etc/apt/sources.list.d/pve-enterprise.sources
sed -i 's/Enabled: yes/Enabled: no/' /etc/apt/sources.list.d/ceph.sources
# OR
echo "Enabled: no" >> /etc/apt/sources.list.d/pve-enterprise.sources
echo "Enabled: no" >> /etc/apt/sources.list.d/ceph.sources


# Add no-subscription repo
wget https://download.proxmox.com/debian/proxmox-release-trixie.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
gpg --keyserver keyserver.ubuntu.com --recv-keys 24B30F06ECC1836A4E5EFECBA7BCD1420BFE778E
gpg --export 24B30F06ECC1836A4E5EFECBA7BCD1420BFE778E > /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
cat > /etc/apt/sources.list.d/pve-no-subscription.sources << 'EOF'
Types: deb
URIs: http://download.proxmox.com/debian/pve
Suites: trixie
Components: pve-no-subscription
Signed-By: /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
Enabled: yes

Types: deb
URIs: http://download.proxmox.com/debian/ceph-squid
Suites: trixie
Components: no-subscription
Signed-By: /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
Enabled: yes
EOF

apt update

# Remove no-supscription warning
sed -i.bak "s/if (data.status !== 'Active')/if (false)/" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js
systemctl restart pveproxy
```