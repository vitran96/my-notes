# Log

I used Proxmox GUI installer
- Default username is root
- Type in password, email, timezone, static IP, hostname

## Network issue

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