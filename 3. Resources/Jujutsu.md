---
para: resource
tags: [tooling]
---
[[Git]] compatible [[Version Control Software|VCS]]

# Init

```shell
# Recent jj colocates with Git by default
jj git init

# On older versions
jj git init --colocate
```

# Commit

The working copy is auto-snapshotted (no `git add`). Describe with `jj describe -m`, then start the next change with `jj new`; or `jj commit -m` does both.

# How to not commit some file

```shell
jj split
```

## Remove file that was just ignored

Add it to `.gitignore`, then `jj file untrack <path>`.

# Undo

```shell
# Undo last "OPERATION" with
jj undo
```

# Change old commit

```shell
# Option 1: new change on top, edit, then fold it into the parent
jj new <rev>
# make change
jj squash

# Option 2: edit the commit directly
jj edit <rev>
```

# Create branch

`bookmark` is the closest thing to [[Git]] branch .

## Create bookmark

```shell
jj bookmark create <name> -r @-
```

## Update bookmark tracking commit

```shell
# @ is the working-copy commit, @- is its parent
jj bookmark move <name> --to @-
# or
jj bookmark set <name> -r @-
```

## Push bookmark

```shell
# Track bookmark
jj bookmark track <name>@origin

jj git push -b <name>
# or push the parent of the working copy with an auto-named bookmark
jj git push -c @-
```

# Config

https://jj-vcs.github.io/jj/latest/config

```shell
# User (or git global) config
jj config set --user <config key> <value>

# Repo
jj config set --repo <config key> <value>
```

## Code sign

```shell
jj config set --user git.sign-on-push true

# signing.behavior: drop / keep / own / force
jj config set --user signing.behavior own
jj config set --user signing.backend gpg
jj config set --user signing.key <id>

# Optional
jj config set --user signing.backends.gpg.program <path>
```

## Config UI for resolve conflict / message

```shell
jj config set --user ui.editor vim
jj config set --user ui.merge-editor <tool>
jj config set --user ui.diff-editor <tool>
```

# Fix command

`jj fix` runs configured formatters (`[fix.tools.<name>]` `command=[...]`, `patterns=["glob:'**/*.java'"]`) over file contents of the chosen revisions (default: mutable ones).

# Run command

Similar to `jj fix`, this allow us to run command on specific commit. %% verify: jj run is not functional in released versions as far as I know %%

# Handle missing email

```shell
jj config set --user user.email <x>
# newer jj
jj metaedit --update-author -r <rev>
# older jj
jj describe --reset-author --no-edit <rev>
```

%% verify which one jj --version supports %%

# Handle missing message

```shell
jj describe -r <rev> -m "<msg>"
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
