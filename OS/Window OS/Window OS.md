General OS used by most people
Good for gaming and [[Dot NET]] app development

# Enable a feature

```bash
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

# Set environment variable

User:

```bash
setx <Name> <Value>
```

System-wide:

```bash
setx /m <Name> <Value>
```

Appending to User PATH:

```bash
for /f "usebackq tokens=2,*" %A in (`reg query HKCU\\Environment /v PATH`) do set my_user_path=%B
setx PATH "%my_user_path%;NEW_PATH"
```

Appending to System PATH:

```bash
for /f "usebackq tokens=2,*" %A in (`reg query "HKLM\\SYSTEM\\ControlSet001\\Control\\Session Manager\\Environment" /v PATH`) do set system_path=%B
setx /M PATH "%my_user_path%;NEW_PATH"
```