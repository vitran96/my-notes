---
para: archive
was: resource
---
[[Window OS]] net configuration
Domain can be
- AzureAD

# Add to "Remote desktop" allowed user
```powershell
net localgroup "Remote Desktop Users" <DOMAIN>\<USER> /add
```

# Add to "docker-users"
```powershell
net localgroup docker-users <DOMAIN>\<USER> /add
```