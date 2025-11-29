<div align="center">

# Ansible Role: SafetyScan

[![Ansible Galaxy](https://img.shields.io/badge/galaxy-yourusername.safetyscan-blue.svg)](https://galaxy.ansible.com/yourusername/safetyscan)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

**Simple Ansible role to install SafetyScan security scanning tool**

[Overview](#-overview) • [Requirements](#-requirements) • [Installation](#-installation) • [Usage](#-usage) • [Examples](#-examples) • [Troubleshooting](#-troubleshooting)

</div>

---

## 📋 Overview

This Ansible role automates the installation of [SafetyScan](https://github.com/IsMohit/SafetyScan-Automated-Security-Scanning-Tool-for-Linux) - a comprehensive SAST & DAST security scanning tool for Linux environments.

### What This Role Does

- ✅ Installs Docker (if not already present)
- ✅ Clones SafetyScan from GitHub
- ✅ Installs SafetyScan globally (`/usr/local/bin/safetyscan`)
- ✅ Skips installation if already present (idempotent)
- ✅ Works on Ubuntu, Debian, CentOS, Fedora


> **Philosophy:** This role focuses solely on installation. Use separate playbooks to run scans with full control over when and how they execute.

---

## 🔧 Requirements

- **Ansible:** 2.9 or higher
- **Target System:** Linux (Ubuntu 18.04+, Debian 10+, CentOS 7+, Fedora 30+)
- **Privileges:** Root/sudo access required
- **Network:** Internet access to download Docker and clone repository

### System Requirements (Target Hosts)

| Component | Minimum |
|-----------|---------|
| RAM | 2 GB |
| Disk Space | 5 GB free |
| Architecture | x86_64 (64-bit) |

---

## 📥 Installation

### From Ansible Galaxy (Recommended)
```bash
ansible-galaxy install yourusername.safetyscan
```

### From GitHub
```bash
ansible-galaxy install git+https://github.com/yourusername/ansible-role-safetyscan.git
```

### Using requirements.yml
```yaml
# requirements.yml
roles:
  - name: yourusername.safetyscan
    version: 1.0.0
```
```bash
ansible-galaxy install -r requirements.yml
```

---

## 🎯 Role Variables

All variables have sensible defaults. Override them as needed.

### Main Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `safetyscan_version` | `main` | Git branch/tag to install |
| `safetyscan_force_install` | `false` | Force reinstall even if present |
| `safetyscan_install_docker` | `true` | Install Docker (skip if already installed) |
| `safetyscan_repo_url` | `https://github.com/IsMohit/...` | Repository URL |
| `safetyscan_install_dir` | `/opt/safetyscan` | Clone destination |
| `safetyscan_bin_dir` | `/usr/local/bin` | Binary installation path |

### Example: Custom Configuration
```yaml
- hosts: servers
  become: yes
  roles:
    - role: ansible-role-safetyscan
      vars:
        safetyscan_version: "v1.0.0"  # Install specific version
        safetyscan_install_docker: false  # Skip Docker if already installed
```

---

## 🚀 Usage

### Quick Start

**Step 1: Install SafetyScan**
```yaml
# playbook: install.yml
---
- name: Install SafetyScan
  hosts: all
  become: yes
  roles:
    - ansible-role-safetyscan
```
```bash
ansible-playbook -i inventory install.yml
```

**Step 2: Run Scans**
```yaml
# playbook: scan.yml
---
- name: Run Security Scan
  hosts: webservers
  become: yes
  tasks:
    - name: Run SAST scan
      command: safetyscan /var/www/myapp --mode sast
```
```bash
ansible-playbook -i inventory scan.yml
```

---

## 📚 Examples

### Example 1: Basic Installation
```yaml
---
- name: Install SafetyScan on all servers
  hosts: all
  become: yes
  roles:
    - ansible-role-safetyscan
```

### Example 2: Skip Docker Installation

If Docker is already installed on your systems:
```yaml
---
- name: Install SafetyScan (Docker already present)
  hosts: all
  become: yes
  roles:
    - role: ansible-role-safetyscan
      vars:
        safetyscan_install_docker: false
```

### Example 3: Force Reinstall

Update to the latest version:
```yaml
---
- name: Update SafetyScan
  hosts: all
  become: yes
  roles:
    - role: ansible-role-safetyscan
      vars:
        safetyscan_force_install: true
```

### Example 4: Install Specific Version
```yaml
---
- name: Install SafetyScan v1.0.0
  hosts: all
  become: yes
  roles:
    - role: ansible-role-safetyscan
      vars:
        safetyscan_version: "v1.0.0"
```

---

## 🔍 Running Scans

After installation, use SafetyScan in your playbooks:

### SAST Scan (Static Analysis)
```yaml
---
- name: Run SAST Scan
  hosts: webservers
  become: yes
  vars:
    project_path: "/var/www/myapp"
  tasks:
    - name: Scan source code
      command: safetyscan {{ project_path }} --mode sast
      args:
        chdir: "{{ project_path }}"
```

### DAST Scan (Dynamic Analysis)
```yaml
---
- name: Run DAST Scan
  hosts: webservers
  become: yes
  vars:
    project_path: "/opt/nodejs-app"
    app_port: 3000
  tasks:
    - name: Scan running application
      shell: |
        safetyscan {{ project_path }} \
          --mode dast \
          --start "npm install && npm start" \
          --port {{ app_port }}
```

### Both SAST + DAST
```yaml
---
- name: Complete Security Scan
  hosts: production
  become: yes
  vars:
    project_path: "/var/www/webapp"
  tasks:
    - name: Run comprehensive scan
      shell: |
        safetyscan {{ project_path }} \
          --mode both \
          --start "npm start" \
          --port 3000
```

### Scan Multiple Projects
```yaml
---
- name: Scan All Applications
  hosts: all
  become: yes
  vars:
    projects:
      - path: "/var/www/frontend"
        mode: "sast"
      - path: "/opt/backend"
        mode: "both"
        start_cmd: "python app.py"
        port: 5000
  tasks:
    - name: Run scans
      shell: |
        {% if item.mode in ['dast', 'both'] %}
        safetyscan {{ item.path }} \
          --mode {{ item.mode }} \
          --start "{{ item.start_cmd }}" \
          --port {{ item.port }}
        {% else %}
        safetyscan {{ item.path }} --mode {{ item.mode }}
        {% endif %}
      loop: "{{ projects }}"
```

---

## 🛠️ Troubleshooting

### Issue: "safetyscan: command not found" during verification

**This is usually OK!** The verification step may fail, but the installation succeeded.

**Check:**
```bash
# Verify binary exists
ls -la /usr/local/bin/safetyscan

# Test manually
/usr/local/bin/safetyscan --help
```

**If it works:** Installation succeeded. The Ansible verification just couldn't find it in the current PATH.

**Fix permanently:**
```bash
# Option 1: Start new shell session
bash

# Option 2: Reload PATH
source ~/.bashrc

# Now try
safetyscan --help
```

---

### Issue: Docker not installed or not working

**Symptoms:**
[safetyscan] ✗ Docker build failed!

**Common Causes & Solutions:**

1. **Missing or invalid dependencies file:**
```bash
   # Check if your project has:
   ls -la package.json      # Node.js
   ls -la requirements.txt  # Python
   ls -la pom.xml          # Java
```

2. **Network issues:**
```bash
   # Test Docker network
   docker pull node:18
```

3. **Insufficient disk space:**
```bash
   df -h
   docker system df
   
   # Clean up if needed
   docker system prune -a
```

4. **Test build manually:**
```bash
   cd /path/to/your/project
   docker build -t test .
```

---

### Issue: Permission denied

**Symptoms:**
Permission denied while trying to connect to the Docker daemon socket

**Solution:**
```bash
# Add your user to docker group
sudo usermod -aG docker $USER

# IMPORTANT: Log out and log back in, or run:
newgrp docker

# Test
docker ps
```

---

### Issue: Role installation fails

**Check Ansible version:**
```bash
ansible --version  # Should be 2.9+
```

**Check network connectivity:**
```bash
# Can you reach GitHub?
ping github.com

# Can you reach Docker repos?
ping download.docker.com
```

**Run with verbose mode:**
```bash
ansible-playbook -i inventory install.yml -vvv
```

---

## 🧪 Testing

### Test Installation
```bash
# Create test playbook
cat > test.yml <<'EOF'
---
- hosts: localhost
  become: yes
  roles:
    - ansible-role-safetyscan
EOF

# Run it
ansible-playbook test.yml
```

### Verify Installation
```bash
# Check binary exists
ls -la /usr/local/bin/safetyscan

# Test command
safetyscan --help

# Test Docker
docker --version
docker ps

# Check logs (if any issues)
journalctl -u docker -n 50
```

---

## 📖 Additional Resources

### SafetyScan Documentation
- [SafetyScan GitHub](https://github.com/IsMohit/SafetyScan-Automated-Security-Scanning-Tool-for-Linux)
- [SafetyScan README](https://github.com/IsMohit/SafetyScan-Automated-Security-Scanning-Tool-for-Linux/blob/main/README.md)

### Ansible Resources
- [Ansible Best Practices](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html)
- [Ansible Galaxy](https://galaxy.ansible.com/)

### Docker Resources
- [Docker Installation](https://docs.docker.com/engine/install/)
- [Docker Security](https://docs.docker.com/engine/security/)

---

## 🤝 Contributing

Contributions are welcome!

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit changes: `git commit -m 'Add amazing feature'`
4. Push to branch: `git push origin feature/amazing-feature`
5. Open a Pull Request


## 👤 Author

**Mohit Khambekar**

- GitHub: [@IsMohit](https://github.com/IsMohit)
- SafetyScan: [Project Link](https://github.com/IsMohit/SafetyScan-Automated-Security-Scanning-Tool-for-Linux)

---

## 🙏 Acknowledgments

- **SafetyScan** - The security scanning tool this role installs
- **Semgrep** - SAST engine
- **OWASP ZAP** - DAST engine
- **Docker** - Containerization platform
- **Ansible Community** - For excellent tooling and documentation

---

## ⭐ Support

If this role helped you, please:
- ⭐ Star the repository
- 🐛 Report issues on [GitHub Issues](https://github.com/IsMohit/ansible-role-safetyscan/issues)
- 📖 Improve documentation via Pull Requests
- 📢 Share with others who might find it useful

---

<div align="center">

[⬆ Back to Top](#ansible-role-safetyscan)

</div>