[[Window OS]] virtual Linux.
It working kind of like [[Docker]].
[[Docker]] image might be adaptable with WSL.

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
wsl --install --d <distribution name>
```

eg:

```powershell
wsl --install --d Ubuntu-18.04
```