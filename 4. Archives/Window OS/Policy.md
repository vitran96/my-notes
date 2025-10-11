# Get
```powershell
Get-ExecutionPolicy -list
```
# Set
```powershell
# ALL & UNRESTRICTED
Set-ExecutionPolicy unrestricted

# CurrentUser & UNRESTRICTED
Set-ExecutionPolicy -ExecutionPolicy unrestricted -Scope CurrentUser
```