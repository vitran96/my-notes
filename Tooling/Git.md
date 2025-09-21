Sample `.gitconfig` file

```toml
[credential "https://github.com"]
    helper = !/usr/bin/gh auth git-credential
[user]
    email = <email>
    name = <name>
    signingkey = <signed commit key>
[filter "lfs"]
    clean = git-lfs clean -- %f
    smudge = git-lfs smudge -- %f
    process = git-lfs filter-process
    required = true
[commit]
    gpgsign = true
```