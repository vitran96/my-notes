# Auto-Configure
It will automatically set current shell env, tooling.

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

# VSCode plugin
Plugin for [[VSCode]]. Not support multi workspace.
Modify extensions runtime path directly in **settings.json** of a project.

