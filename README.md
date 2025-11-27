# Ansible Role: SafetyScan

Simple Ansible role to install SafetyScan security scanning tool.

## What This Role Does

- ✅ Installs Docker (if not already installed)
- ✅ Installs SafetyScan from GitHub
- ✅ Skips installation if already present
- ✅ Simple and straightforward

## Requirements

- Ansible 2.9+
- Target system: Linux (Ubuntu, Debian, CentOS, Fedora)
- Sudo/root access

## Role Variables
```yaml
# Installation settings
safetyscan_version: "main"  # Git branch/tag to install
safetyscan_force_install: false  # Set to true to reinstall even if present

# Docker installation
safetyscan_install_docker: true  # Set to false if Docker already installed
```

## Example Usage

### 1. Install SafetyScan
```yaml
# playbook: install-safetyscan.yml
- hosts: servers
  become: yes
  roles:
    - ansible-role-safetyscan
```

Run it:
```bash
ansible-playbook -i inventory install-safetyscan.yml
```

### 2. Run Scans (Separate Playbook)
```yaml
# playbook: run-scan.yml
- hosts: servers
  become: yes
  tasks:
    - name: Run SAST scan
      command: safetyscan /path/to/project --mode sast
      args:
        chdir: /path/to/project
```

Run it:
```bash
ansible-playbook -i inventory run-scan.yml
```

## Installation
```bash
# From Galaxy (once published)
ansible-galaxy install IsMohit.safetyscan

# From GitHub
ansible-galaxy install git+https://github.com/IsMohit/ansible-role-safetyscan.git
```

## License

MIT

## Author

Mohit Khambekar