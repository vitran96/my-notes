---
repo: https://github.com/jdx/mise
---

# Auto-Configure
It will automatically set current shell env, tooling.
Config for `mise.toml`: https://mise.jdx.dev/configuration.html

# Tasks
Not cross-platform so be careful when write.
[Document](https://mise.jdx.dev/tasks/task-configuration.html)

# Watch
A very convenient built-in watch support for language not support it.
Require `watchexec` binary.
[Document](https://mise.jdx.dev/cli/watch.html)

# Install / Search tool
```shell
# If already has mise config
mise install

# If install tool only
mise install <tool>@<version>

# Install & Use globally
mise use -g <tool>@<version>

# Install & Use in project
mise use <tool>@<version>

# Search tool
mise search <input>

# Search version
mise ls-remote <tool name>
```

# Self update
```shell
mise self-update
```

# Monorepo tasks
[[GitHub]] issue: https://github.com/jdx/mise/discussions/6564

## Sample commands
```shell
# Run tests in ALL projects
mise //...:test

# Run all build tasks under services/
mise //services/...:build

# Run ALL tasks in frontend (wildcards need quotes)
mise '//projects/frontend:*'

# Run all test:* tasks everywhere
mise '//...:test:*'
```

## Getting started
Set `export MISE_EXPERIMENTAL=1`
```toml
# root mise config
experimental_monorepo_root = true

[tools]
node = "20"
python = "3.12"

[tasks."start:be"]
depends = { "//server:start" }

# server mise config
[tools]
node = "21"

[tasks.start]
run = "npm start"
```

# ENV config
Use `MISE_ENV=<env name>` to switch mise config file.
Rule is similar to [[Vite]] env file rule, the last one will override the 1st one:
1. mise.toml
2. mise.local.toml
3. mise.<MISE_ENV>.toml
4. mise.<MISE_ENV>.local.toml

# Lock file
Similar to [[NodeJS]] package-lock file, mise can enable that.
Without this, we will have to manually choose version.
To enable
```toml
[settings]
lockfile = true
```

# VSCode plugin
Plugin for [[VSCode]]. Not support multi workspace.
Modify extensions runtime path directly in **settings.json** of a project.

## Auto config other extensions
This plugin, if enable, also auto config other extensions. Use local shims config can help but will fail on [[Window OS]].