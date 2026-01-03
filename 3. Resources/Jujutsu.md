[[Git]] compatible [[Version Control Software|VCS]]

# Init

```shell
# JJ Init but git command will not work
jj init

# JJ init with git
jj git init --colocate
```

# Commit

By default, JJ auto `git add` all file(s).
Unlike [[Git]], we do add -> commit, JJ will set commit message 1st (or later) and commit very thing in that is currently changed with `jj new [-m next-commit-message]`

# How to not commit some file

```shell
jj split
```

## Remove file that was just ignored

%% TODO: %%

# Undo

```shell
# Undo last "OPERATION" with
jj undo
```

# Change old commit

```shell
jj new <revision>
# make change
jj squash --to <revision> --from <new revision>
```

# Create branch

`bookmark` is the closest thing to [[Git]] branch .

## Create bookmark

```shell
jj b c >name>
```

## Update bookmark tracking commit

```shell
# @ for latest un-commited change
# @ can have alternative like HEAD if any
# minus mean last commit
jj b m @-
```

## Push bookmark

```shell
# Track bookmark
jj b t <branch name>

jj git push --to <branch_name>c
```

# Config

```shell
# User (or git global) config
jj config --user <config key> <value>

# Repo
jj config --repo ...
```

# Config

https://jj-vcs.github.io/jj/latest/config

## Code sign

```shell
jj config --user git.sign-on-push true

jj config --user signing.behaviour drop
jj config --user signing.? gpg
jj config --user signing.key <...>

# Optional?
jj config --user signing.gpg.program ...
```

## Config UI for resolve conflict / message

```shell
jj config --user ui.? vim
jj config --user ui.? vim
```

# Fix command

This command is similar t [[Maven]] `goal`. Eg: we can config JJ tools for fix and then run `jj fix` to run them.
Eg: run code format tool.
We can choose which commit to run the tools on.

# Run command

Similar to `jj fix`, this allow us to run command on specific commit. 

# Handle missing email

Not the best approach yet but currently work
```shell
jj new <commit before missing email>
jj merge -s <commit with missing email> -d @-
```

# Handle missing message

```shell
# I have not tried this yet
jj desc <commit>
```

# Command table compare with [[Git]]

%% TODO: %%

# Merge

```shell
jj new @ <branch / revision>

# or merge to previous revision then create a merge commit
jj new @- <branch / revision>
jj commit -m <message>
```