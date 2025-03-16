# Boot with shell

Append "init=/bin/sh" into cmdline.txt in boot drive

# User related

## Delete user

```bash
sudo deluser -remove-home <USER>
```

## Change password

```bash
sudo passwd <USER>
```

## Change user

```bash
sudo -i -u <USER>
```

## Execute as user

```bash
sudo -u <USER> <SCRIPT>
```

# Execute online script

Fetch and execute script with arguments
```bash
curl <http://example.com/script.sh> | bash -s -- arg1 arg2
```

# Replace text in file

```bash
sed -i 's/old-text/new-text/g' input.txt
```

# Symlink

```bash
ln -s [OPTIONS] FILE LINK
```

# Count file

```bash
wc -l
```

Eg:

```bash
ls | wc -l
```

# Packaging to tar.gz

```bash
tar -czvf filename.tar.gz /path/to/dir1 dir2 file1 file2
```

Install

```bash
tar -C / -xzvf file.tar.gz
```

# Linux password hash

Format: `$<HashAlgorithmType>$<Salt>$<PasswordHash>`

Eg: `$6$aaaa$bbbb`

- $6 -> SHA512
- $aaaa -> salt is "aaaa"
- $bbbb -> password hash is bbbb

Linux password hash is save in `/etc/shadow`

# Change Linux password

1. On a Linux device, insert other Linux drive
2. Mount the other Linux drive
3. Sudo access to shadow file in the other drive
4. Generate a new hash password and replace the existing hash of a user of your choice

Generete hash password from python (only on a Linux device):

```python
import crypt
salt="yourSalt"
crypt.crypt("yourNewPassword", salt)
```

