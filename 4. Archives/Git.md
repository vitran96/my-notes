# Git Credential helper

Git can use the federated AWS credentials using the credential helper. Configure git to use the credential helper.

```sh
git config --local credential.<https://git-codecommit.eu-west-1.amazonaws.com.helper> "!aws codecommit credential-helper --profile <profile> $@"

git config --local credential.<https://git-codecommit.eu-west-1.amazonaws.com.UseHttpPath> true
```

If you are using the Git Credential Manager then remove it or the credential helper will not work correctly. You can add a credential helper for the DevStack or Github to store your username and password. The commands must be executed in a shell with administrator rights.

```sh
git config --system --unset credential.helper

git config --system credential.<https://devstack.vwgroup.com.username> <Volkswagen ID>

git config --system credential.<https://devstack.vwgroup.com.helper> store

git config --system credential.<https://github.com.username> <Github username>

git config --system credential.<https://github.com.helper> store
```

You can configure the Git Credential Manager for Windows for non AWS CodeCommit repositories. Then username and password are stored in the Windows credential store.

```sh
git config --local credential.helper "manager"
```

# Gitconfig file
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