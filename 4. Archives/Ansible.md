# Install

```shell
pipx install ansible ansible-lint ansible-galaxy
```

# Standard project structure

```plaintext
# ==============================================================================
# STANDARD ANSIBLE PROJECT STRUCTURE (Role-Based / Best Practices)
# ==============================================================================
# This layout follows Ansible's official recommendations for organizing playbooks,
# group variables, and reusable roles.
#
ansible-project/
├── ansible.cfg              # Ansible configuration file (overrides default /etc/ansible/ansible.cfg)
├── hosts.ini                # Simple static inventory file (or dynamic inventory scripts)
├── .ansible-lint
├── requirements.yml
├── group_vars/              # Variables applied to specific host groups
│   ├── all.yml              # Variables applied to all hosts
│   ├── webservers.yml       # Variables applied only to [webservers] inventory group
│   └── dbservers.yml        # Variables applied only to [dbservers] inventory group
├── host_vars/               # Variables applied to individual specific hosts
│   └── db-primary-01.yml
├── site.yml                 # Master playbook that imports other playbooks
├── webservers.yml           # Playbook specifically targeting webservers
├── dbservers.yml            # Playbook specifically targeting database servers
└── roles/                   # Reusable, self-contained units of configuration
    ├── common/              # Tasks that run on all servers (e.g., NTP, base packages)
    │   ├── tasks/
    │   │   └── main.yml     # Main list of tasks for the role
    │   ├── handlers/
    │   │   └── main.yml     # Handlers (e.g., service restarts) triggered by tasks
    │   ├── templates/
    │   │   └── ntp.conf.j2  # Jinja2 templates to be populated with variables
    │   ├── files/
    │   │   └── resolv.conf  # Static files to be copied to remote servers
    │   ├── vars/
    │   │   └── main.yml     # Role-specific internal variables (high priority)
    │   ├── defaults/
    │   │   └── main.yml     # Default variables for the role (low priority, easily overridden)
    │   └── meta/
    │       └── main.yml     # Role dependencies and author metadata
    └── webserver/           # Another role for web server configuration
        ├── tasks/
        │   └── main.yml
        └── templates/
            └── nginx.conf.j2
```