# Init
```shell
# JJ Init but git command will not work
jj init

## JJ init with git
jj git init --colocate
```

# Commit
By default, JJ auto `git add` all file(s).
Unlike [[git]], we do add -> commit, JJ will set commit message 1st (or later) and commit very thing in that is currently changed with `jj new [-m next-commit-message]`

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
%% TODO: %%

# Create branch
`bookmark` is the closest thing to [[git]] branch .

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