---
para: resource
tags: [homelab]
---
Home-lab virtualisation host. See also: [[OpenTofu]], [[pfsense]], [[2026-06 Setup home server]]

# Enable snippets

For [[Terraform]] to use other [[VM]] as snippet: `pvesm set local --content iso,vztmpl,backup,snippets` or "**Datacenter → Storage → local → Edit → Content → check "Snippets"**."

# Permission Denied Error (403 Forbidden)

*   **Error Message:** `Permission check failed (/storage/local, Datastore.Audit|Datastore.AllocateSpace)`
*   **Cause:** The Proxmox API token being used by Terraform does not have the necessary privileges to perform the requested actions, such as uploading files (snippets, ISOs) to a storage pool.
*   **Solution:**
    1.  Log in to the Proxmox web UI.
    2.  Go to `Datacenter` -> `Permissions` -> `Roles` and create a new role (e.g., `Terraform`) with a comprehensive set of permissions required for VM management (`Datastore.Audit`, `Datastore.AllocateSpace`, `VM.Allocate`, `VM.Clone`, `VM.Config.*`, `VM.PowerMgmt`, etc.).
    3.  Go to `Datacenter` -> `Permissions` and assign this new role to a user (e.g., `root@pam` or a dedicated `terraform-user@pve`) for the path `/`.
    4.  Generate a new API token for that user and update the `proxmox_api_token` variable with the new token secret.

---

# Hostname Lookup Error (500 Internal Server Error)

*   **Error Message:** `hostname lookup 'pve' failed - failed to get address info for: pve: Name or service not known`
*   **Cause:** The Proxmox server itself is unable to resolve its own hostname to an IP address. This is a local name resolution issue on the Proxmox node.
*   **Solution:**
    1.  SSH into the Proxmox server as `root`.
    2.  Check the hosts file: `cat /etc/hosts`.
    3.  Check what is the domain name and correct it in the config
    4.  Run again

---

# ISO Volume Not Found Error

*   **Error Message:** `volume 'local:iso/pfSense-CE-2.7.2-RELEASE-amd64.iso' does not exist`
*   **Cause:** The VM resource is configured to use an ISO file that is not present in the specified Proxmox storage pool.
*   **Solution:** Instead of manually uploading the ISO, the Terraform configuration was updated to manage the file automatically. The `proxmox_virtual_environment_file` resource is used to upload the ISO from a local path on the machine running Terraform. The VM's `cdrom` block then references the ID of this managed file resource, creating a dependency and ensuring the file is uploaded before the VM is created.

---

# Using Compressed ISOs (`.iso.gz`)

*   **Question:** Can a gzipped ISO file (`.iso.gz`) be used directly?
*   **Answer:** No. Proxmox requires a standard, uncompressed `.iso` file to mount as a virtual CD-ROM. The `.iso.gz` file must be decompressed on your local machine (e.g., using `gunzip`) before running `tofu apply`. The `pfsense_iso_path` variable must point to the resulting `.iso` file.

# Initialization

Cloud-init: only works with cloud-init-enabled images; configure via user-data (snippets).

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
ip addr

lsusb


# Try connect to online resource
ping 8.8.8.8
nslookup google.com

systemctl restart networking
```

Setup the interface
``` file="/etc/network/interfaces"
auto lo
iface lo inet loopback

auto enx00000
iface enx00000 inet manual

auto vmbr0
iface vmbr0 inet static
    address 192.168.1.X/24
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
Err:6 https://enterprise.proxmox.com/debian/ceph-squid trixie InRelease
Err:7 https://enterprise.proxmox.com/debian/pve trixie InRelease
```

Fix
```shell
# Disable enterprise repos
sed -i 's/Enabled: yes/Enabled: no/' /etc/apt/sources.list.d/pve-enterprise.sources
sed -i 's/Enabled: yes/Enabled: no/' /etc/apt/sources.list.d/ceph.sources


# Add no-subscription repo (keyring ships in proxmox-archive-keyring) %% verify on PVE 9 %%
cat > /etc/apt/sources.list.d/pve-no-subscription.sources << 'EOF'
Types: deb
URIs: http://download.proxmox.com/debian/pve
Suites: trixie
Components: pve-no-subscription
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
Enabled: yes

Types: deb
URIs: http://download.proxmox.com/debian/ceph-squid
Suites: trixie
Components: no-subscription
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
Enabled: yes
EOF

apt update

# Remove no-supscription warning (overwritten on every proxmox-widget-toolkit update)
sed -i.bak "s/if (data.status !== 'Active')/if (false)/" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js
systemctl restart pveproxy
```
