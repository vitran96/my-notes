---
para: archive
was: resource
---
[[Window OS]] subsystem for Linux. WSL 2 runs a real Linux kernel in a lightweight utility VM (WSL 1 translated syscalls). [[Docker]] Desktop uses WSL 2 as its backend.

# Install / Update
```powershell
wsl --install
wsl --update
```
# Online distribution

```powershell
wsl --list --online
```

# Install distribution

```powershell
wsl --install -d <distribution name>
```

eg:

```powershell
wsl --install -d Ubuntu-18.04
```