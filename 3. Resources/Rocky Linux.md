---
para: resource
tags: [linux]
---
Downstream rebuild of [[RHEL]], created when CentOS Linux was replaced by CentOS Stream (alternative: AlmaLinux). See [[Linux Distro]], [[Fedora]].

# [[restorecon]] for binaries used by a systemd service

SELinux: a binary copied/moved from e.g. a home dir keeps its old label (`user_home_t`), so systemd is denied exec. `restorecon -v /path/to/binary` resets the default context (applies to all RHEL-family incl. Fedora).
