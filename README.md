# Ansible Role: SafetyScan

[![Ansible Galaxy](https://img.shields.io/badge/galaxy-yourusername.safetyscan-blue.svg)](https://galaxy.ansible.com/yourusername/safetyscan)
[![License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE)

Automate the installation and execution of SafetyScan - a comprehensive SAST & DAST security scanning tool for Linux environments.

## Features

- ✅ Automatic Docker installation and configuration
- ✅ Python 3 installation for comprehensive reports
- ✅ SafetyScan installation from GitHub
- ✅ Immediate scan execution (no cron required)
- ✅ Scheduled scans with cron (optional)
- ✅ Slack/Email notifications (optional)
- ✅ Multiple project support
- ✅ Supports Ubuntu, Debian, CentOS, Fedora, Arch Linux

## Requirements

- Ansible 2.9 or higher
- Target systems: Linux (Ubuntu, Debian, CentOS, Fedora, Arch)
- Minimum 2GB RAM, 5GB disk space on target hosts

## Role Variables

### Basic Configuration
```yaml
# Installation settings
safetyscan_version: "main"  # Git branch/tag
safetyscan_install_dir: "/opt/safetyscan"
safetyscan_install_docker: true
safetyscan_install_python: true

# Immediate scan execution (default: false)
safetyscan_run_immediately: false

# Projects to scan
safetyscan_projects: []
```

### Example: Immediate Scan (No Cron/Notifications)
```yaml
safetyscan_run_immediately: true
safetyscan_projects:
  - name: "my-webapp"
    path: "/var/www/myapp"
    mode: "both"
    start_cmd: "npm start"
    port: 3000
```

### Example: Scheduled Scans with Notifications
```yaml
safetyscan_projects:
  - name: "my-webapp"
    path: "/var/www/myapp"
    mode: "both"
    start_cmd: "npm start"
    port: 3000
    schedule: "0 2 * * *"  # Daily at 2 AM

safetyscan_enable_notifications: true
safetyscan_notification_slack_webhook: "https://hooks.slack.com/..."
```

### All Available Variables

See `defaults/main.yml` for complete list of variables.

## Dependencies

None.

## Example Playbooks

### 1. Install Only (No Scan)
```yaml
- hosts: servers
  become: yes
  roles:
    - role: ansible-role-safetyscan
```

### 2. Install and Run Immediate Scan
```yaml
- hosts: webservers
  become: yes
  roles:
    - role: ansible-role-safetyscan
      vars:
        safetyscan_run_immediately: true
        safetyscan_projects:
          - name: "production-app"
            path: "/var/www/app"
            mode: "sast"
```

### 3. Install and Schedule Regular Scans
```yaml
- hosts: webservers
  become: yes
  roles:
    - role: ansible-role-safetyscan
      vars:
        safetyscan_projects:
          - name: "app1"
            path: "/var/www/app1"
            mode: "both"
            start_cmd: "npm start"
            port: 3000
            schedule: "0 2 * * 1"  # Monday 2 AM
```

### 4. Multi-Project Setup
```yaml
- hosts: all
  become: yes
  roles:
    - role: ansible-role-safetyscan
      vars:
        safetyscan_run_immediately: true
        safetyscan_projects:
          - name: "frontend"
            path: "/var/www/frontend"
            mode: "sast"
          - name: "backend-api"
            path: "/opt/api"
            mode: "both"
            start_cmd: "python app.py"
            port: 5000
          - name: "microservice"
            path: "/opt/service"
            mode: "dast"
            start_cmd: "java -jar app.jar"
            port: 8080
```

## License

MIT

## Author

Mohit Khambekar

## Support

- Issues: [GitHub Issues](https://github.com/IsMohit/SafetyScan-Automated-Security-Scanning-Tool-for-Linux/issues)
- Galaxy: [Ansible Galaxy](https://galaxy.ansible.com/IsMohit/safetyscan)