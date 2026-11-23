---
para: archive
was: resource
---
# Install from cli
[https://www.codeproject.com/Articles/16767/How-to-Pass-Command-Line-Arguments-to-MSI-Installe](https://www.codeproject.com/Articles/16767/How-to-Pass-Command-Line-Arguments-to-MSI-Installe)

## Snippets

Syntax:

```bash
msiexec.exe /i <Package> <Options>
```

Eg:

```bash
msiexec.exe /i "C:\path\to\setup.msi" /passive /norestart
```

Quiet install:

```powershell
Start-Process "msiexec.exe" -ArgumentList '/i <Package> <Options>' -Wait
```

Full logging:

```bash
msiexec /i <Package> /qb /norestart /l*v <Log file>
```

Pass Parameters - Eg:

```bash
msiexec /i <Package> /qb /norestart Parameter1=one Parameter2=two
```