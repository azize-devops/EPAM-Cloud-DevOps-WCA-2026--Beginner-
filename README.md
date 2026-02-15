# EPAM Cloud & DevOps Fundamentals - Beginner Track

<div align="center">

![DevOps](https://img.shields.io/badge/DevOps-Fundamentals-blue?style=for-the-badge&logo=dev.to&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-CentOS-yellow?style=for-the-badge&logo=linux&logoColor=white)
![Git](https://img.shields.io/badge/Git-Version%20Control-F05032?style=for-the-badge&logo=git&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-Scripting-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-Scripting-5391FE?style=for-the-badge&logo=powershell&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL%20Server-Database-CC2927?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)

**A comprehensive self-study guide for Cloud & DevOps fundamentals**

[Getting Started](#-getting-started) •
[Modules](#-course-modules) •
[Structure](#-repository-structure) •
[Best Practices](#-best-practices) •
[Contributing](#-contributing)

</div>

---

## 📋 Overview

This repository contains practical tasks and detailed answer guides for the **EPAM Cloud & DevOps Winter Camp Academy 2026** - Beginner Track. Each module includes hands-on exercises designed to build foundational skills in DevOps practices.

### What's Included

- ✅ Step-by-step task instructions
- ✅ Comprehensive answer guides with commands
- ✅ Screenshot placeholders for documentation
- ✅ Self-review checklists
- ✅ Troubleshooting sections
- ✅ Command reference tables
- ✅ **Best practices for DevOps workflows**

---

## 🚀 Getting Started

### Prerequisites

- VirtualBox or VMware Workstation
- CentOS 7/8 ISO image
- Git installed on your system
- GitHub account
- Basic understanding of command line

### How to Use This Repository

1. **Clone the repository**
   ```bash
   git clone https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-.git
   ```

2. **Navigate to a module**
   ```bash
   cd "EPAM-Cloud-DevOps-WCA-2026--Beginner-/CentOS Linux/CentOS Linux Essentials"
   ```

3. **Read the task file** (`task.txt`) and complete the exercises

4. **Check your work** against the answer guide (`answer.txt`)

---

## 📚 Course Modules

### 1️⃣ Hypervisor Essentials

| Topic | Description |
|-------|-------------|
| Virtualization Introduction | VirtualBox installation, VM creation, snapshots, networking modes |

<details>
<summary>📖 Topics Covered</summary>

- Type 1 vs Type 2 Hypervisors
- Virtual Machine creation and configuration
- Network adapter types (NAT, Bridged, Host-Only, Internal)
- Snapshots and VM management
- Resource allocation (CPU, RAM, Storage)

</details>

---

### 2️⃣ Networking

| Topic | Description |
|-------|-------------|
| Network Services | DNS, DHCP, HTTP/HTTPS fundamentals |
| Subnets and Routing | IP addressing, subnetting, routing protocols |

<details>
<summary>📖 Topics Covered</summary>

- OSI Model (7 Layers)
- TCP/IP Protocol Suite
- IP Addressing and Subnetting
- CIDR Notation
- Common Network Services
- Routing fundamentals

</details>

---

### 3️⃣ CentOS Linux

#### CentOS Linux Essentials (14 Topics)

| # | Topic | Key Skills |
|---|-------|------------|
| 1 | Installation & Basic Configuration | CentOS installation, initial setup |
| 2 | Basic Shell Usage | Terminal navigation, basic commands |
| 3 | Linux Directories Layout | FHS, directory structure |
| 4 | Working with Files & Links | File operations, symbolic/hard links |
| 5 | Linux Users and Groups | User management, permissions |
| 6 | File Permissions | chmod, chown, umask |
| 7 | Processes Hierarchy | ps, top, process management |
| 8 | Memory Diagnostics | free, /proc/meminfo, memory analysis |
| 9 | CPU Diagnostics | top, htop, uptime, load average |
| 10 | Systemd | Service management, unit files |
| 11 | Using Journalctl | Log analysis, filtering |
| 12 | Software Management | yum, EPEL repository |
| 13 | Network Configuration | IP configuration, /etc/hosts |
| 14 | SSH Overview & Clients | SSH keys, scp, ssh config |

#### Linux Networking Basics

| Topic | Description |
|-------|-------------|
| Advanced Networking | Multi-VM network setup, DHCP server, routing, firewall, NAT |

<details>
<summary>📖 Network Lab Architecture</summary>

```
                    [Internet]
                        │
                      [Host]
                        │
                      Net1
                        │
    [Client_1]     [Server_1]     [Client_2]
        │              │              │
        └────Net2──────┼──────Net3────┘
                       │
        └─────────────Net4────────────┘
```

- Static IP configuration
- DHCP server setup
- Routing between networks
- Firewall rules (iptables)
- NAT configuration

</details>

---

### 4️⃣ Version Control with Git

Progressive difficulty levels inspired by classic game difficulty settings:

| Level | Topic | Skills |
|-------|-------|--------|
| 🟢 | **I Can Win** | Git basics, SSH keys, commits, push/pull |
| 🟡 | **Bring It On** | Branching, .gitignore, merge conflicts |
| 🟠 | **Hurt Me Plenty** | Tags, rebasing, commit history |
| 🔴 | **Hardcore** | Remote management, multiple repositories |
| ⚫ | **Nightmare!** | CLI-only challenge (no GUI allowed) |

<details>
<summary>📖 Skills Progression</summary>

**I Can Win**
- Git installation and configuration
- SSH key generation and GitHub setup
- Basic workflow: add, commit, push, pull

**Bring It On**
- Creating and managing branches
- Configuring .gitignore
- Resolving merge conflicts

**Hurt Me Plenty**
- Creating and managing tags
- Rebasing branches
- Understanding commit history

**Hardcore**
- Managing remote repositories
- Changing remote URLs
- Pushing to multiple repositories

**Nightmare!**
- Complete all tasks using only command line
- No GUI editors or file managers
- Master vim/vi for text editing

</details>

---

### 5️⃣ Scripting: Bash

| # | Topic | Key Skills |
|---|-------|------------|
| 1 | What is Bash | Shell basics, terminal fundamentals |
| 2 | Running Commands | Command execution, PATH |
| 3 | Scripts | Script creation, shebang, execution |
| 4 | Variables | Variable declaration, usage |
| 5 | Environment Variables | PATH, export, env |
| 6 | Special Variables | $?, $$, $!, $0, $# |
| 7 | Conditional Operators | -eq, -ne, -lt, -gt, -f, -d |
| 8 | If Statement | if/elif/else syntax |
| 9 | Case Statement | Pattern matching with case |
| 10 | Pipelines & Logical Operators | \|, &&, \|\|, ; |
| 11 | For Loop | Iteration with for |
| 12 | While Loop | Conditional loops |
| 13 | Until Loop | Until condition is true |
| 14 | Positional Arguments | $1, $2, $@, $* |
| 15 | Input-Output | read, echo, redirection |
| 16 | Functions | Function definition, return values |

<details>
<summary>📖 Topics Covered</summary>

- Shell basics and script creation
- Variables and environment
- Conditional statements (if, case)
- Loops (for, while, until)
- Functions and modularity
- Input/Output handling
- Pipelines and operators

</details>

---

### 6️⃣ Python Essentials

| # | Topic | Key Skills |
|---|-------|------------|
| 1 | Hello, Python! | Python basics, syntax, first program |
| 2 | Virtual Environment | venv, pip, dependencies |
| 3 | Datatypes and Basic Operations | int, str, float, operators |
| 4 | Lists, Sets, Tuples and Dicts | Data structures |
| 5 | Final Task | Practical project |

---

### 7️⃣ PowerShell Essentials

| # | Topic | Key Skills |
|---|-------|------------|
| 1 | PowerShell Console | Console basics, ISE |
| 2 | PowerShell Commands | Cmdlets, Get-Help |
| 3 | Variable Types and Declarations | Variables, types |
| 4 | Arrays and Hash Tables | Collections |
| 5 | Object Properties and Methods | Object manipulation |
| 6 | String Manipulations | String operations |
| 7 | Operators and Regular Expressions | Operators, regex |
| 8 | Conditional and Looping Statements | if, switch, loops |
| 9 | Pipelines | Pipeline processing |
| 10 | Output and Format View | Formatting output |
| 11 | Providers | PS providers |
| 12 | Manage Data Structured File Formats | JSON, XML, CSV |
| 13 | WMI and CIM | System management |
| 14 | Fundamentals of PowerShell Functions | Functions |
| 15 | Advanced Functions and Scripts | Advanced scripting |

---

### 8️⃣ Windows Server 2019

| Topic | Description |
|-------|-------------|
| Deploy Simple Corporate Network Architecture | AD, DNS, DHCP, File Server |

---

### 9️⃣ DB Essentials: MS SQL

| # | Topic | Key Skills |
|---|-------|------------|
| 1 | MSSQL Server Installation | SQL Server setup |
| 2 | Working with Databases and Storage | Database management |
| 3 | Restoring SQL Server Databases | Backup and restore |
| 4 | Importing and Exporting Data | Data transfer |
| 5 | Tracing SQL Server Activity | Monitoring |
| 6 | Managing SQL Server Security | Security configuration |
| 7 | Auditing Data Access and Encrypting Data | Audit and encryption |
| 8 | Performing Database Maintenance | Maintenance tasks |
| 9 | Automating SQL Server Management | Automation |
| 10 | Monitoring SQL Server Using Alerts | Alerts and notifications |

---

## 📁 Repository Structure

```
EPAM-Cloud-DevOps-WCA-2026--Beginner-/
│
├── 📁 1_Hypervisor Essentials/
│   └── 📁 Virtualization Introduction/
│
├── 📁 2_Networking/
│   ├── 📁 1_Subnets and routing with protocols/
│   └── 📁 2_Network Services/
│
├── 📁 3_CentOS Linux/
│   ├── 📁 1_CentOS Linux Essentials/
│   │   ├── 📁 Basic Shell Usage/
│   │   ├── 📁 CentOS Installation and Basic Configuration/
│   │   ├── 📁 CPU Diagnostics/
│   │   ├── 📁 File Permission/
│   │   ├── 📁 Linux Directories Layout/
│   │   ├── 📁 Linux Users and Groups/
│   │   ├── 📁 Memory Diagnostics/
│   │   ├── 📁 Network Configuration/
│   │   ├── 📁 Processes Hierarchy/
│   │   ├── 📁 Software Management/
│   │   ├── 📁 SSH Overview, SSH Clients/
│   │   ├── 📁 Systemd/
│   │   ├── 📁 Using Journalctl/
│   │   └── 📁 Working with Files, Links/
│   │
│   └── 📁 2_Linux Networking Basics/
│
├── 📁 4_Version Control with GIT/
│   ├── 📁 1_I Can Win/
│   ├── 📁 2_Bring It On/
│   ├── 📁 3_Hurt Me Plenty/
│   ├── 📁 4_Hardcore/
│   └── 📁 5_Nightmare!/
│
├── 📁 5_Scripting-Bash/
│   ├── 📁 1_What is Bash/
│   ├── 📁 2_Running commands/
│   ├── 📁 3_Scripts/
│   ├── 📁 ... (16 topics)/
│   └── 📁 16_Functions/
│
├── 📁 6_Python Essentials/
│   ├── 📁 1_Hello, Python!/
│   ├── 📁 2_Virtual Environment/
│   ├── 📁 3_Datatypes and Basic Operations/
│   ├── 📁 4_Lists, Sets, Tuples and Dicts/
│   └── 📁 5_Final Task/
│
├── 📁 7_Scripting-PowerShell Essentials/
│   ├── 📁 1_Powershell Console/
│   ├── 📁 ... (15 topics)/
│   └── 📁 15_Advanced Functions and Scripts/
│
├── 📁 8_Windows Server 2019/
│   └── 📁 1_Deploy Simple Corporate Network Architecture/
│
├── 📁 9_DB Essentials MS SQL/
│   ├── 📁 1_MSSQL Server Installation/
│   ├── 📁 ... (10 topics)/
│   └── 📁 10_Monitoring SQL Server Using Alerts and Notifications/
│
├── 📄 .gitignore
└── 📄 README.md
```

---

## 📝 File Format

Each topic folder contains:

| File | Purpose |
|------|---------|
| `task.txt` | Exercise instructions and requirements |
| `answer.txt` | Detailed solutions with commands and explanations |

### Answer File Features

- **Step-by-step instructions** with expected outputs
- **Screenshot placeholders** for documentation
- **Multiple methods** for completing tasks
- **Command reference tables**
- **Troubleshooting guides**
- **Self-review checklists**

---

## 🛠️ Technologies Covered

<div align="center">

| Category | Technologies |
|----------|--------------|
| **Virtualization** | VirtualBox, VMware |
| **Operating Systems** | CentOS 7/8, RHEL, Windows Server 2019 |
| **Version Control** | Git, GitHub |
| **Networking** | TCP/IP, DNS, DHCP, Firewall, NAT |
| **Shell/Scripting** | Bash, PowerShell, Vi/Vim |
| **Programming** | Python 3 |
| **Databases** | Microsoft SQL Server |
| **Services** | systemd, SSH, httpd, nginx, Active Directory |

</div>

---

## 📈 Learning Path

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              BEGINNER TRACK                                  │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   1. Hypervisor    ──►   2. Networking    ──►   3. CentOS Linux              │
│      Essentials             Fundamentals           Essentials                │
│                                                                              │
│                                    │                                         │
│                                    ▼                                         │
│   ┌──────────────────────────────────────────────────────────────┐           │
│   │                                                              │           │
│   │    4. Git          5. Bash          6. Python                │           │
│   │    Version    ──►  Scripting   ──►  Essentials               │           │
│   │    Control                                                   │           │
│   │                                                              │           │
│   └──────────────────────────────────────────────────────────────┘           │
│                                    │                                         │
│                                    ▼                                         │
│   ┌──────────────────────────────────────────────────────────────┐           │
│   │                                                              │           │
│   │    7. PowerShell      8. Windows        9. SQL Server        │           │
│   │    Scripting     ──►  Server 2019  ──►  Database             │           │
│   │                                                              │           │
│   └──────────────────────────────────────────────────────────────┘           │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## 💡 Best Practices

### Git & Version Control

| Practice | Description |
|----------|-------------|
| **Commit Often** | Make small, focused commits that do one thing well |
| **Write Meaningful Messages** | Use clear, descriptive commit messages (e.g., `fix: resolve login timeout issue`) |
| **Branch Naming** | Use consistent naming: `feature/`, `bugfix/`, `hotfix/` prefixes |
| **Never Commit Secrets** | Use `.gitignore` for credentials, API keys, and sensitive data |
| **Pull Before Push** | Always `git pull` before pushing to avoid conflicts |
| **Review Before Commit** | Use `git diff` to review changes before committing |
| **Use .gitignore** | Exclude build artifacts, logs, and environment-specific files |

```bash
# Good commit message format
git commit -m "feat: add user authentication module"
git commit -m "fix: resolve memory leak in background process"
git commit -m "docs: update installation instructions"
```

### Linux & Shell

| Practice | Description |
|----------|-------------|
| **Use Absolute Paths** | Prefer `/home/user/script.sh` over relative paths in scripts |
| **Check Command Success** | Always verify command exit codes (`$?`) |
| **Backup Before Editing** | Create backups before modifying config files |
| **Use sudo Wisely** | Only use root privileges when necessary |
| **Document Changes** | Comment your changes in configuration files |
| **Test in Safe Environment** | Test commands on non-production systems first |

```bash
# Backup before editing
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak

# Check command success
if [ $? -eq 0 ]; then
    echo "Command succeeded"
else
    echo "Command failed"
fi
```

### Bash Scripting

| Practice | Description |
|----------|-------------|
| **Add Shebang** | Always start with `#!/bin/bash` |
| **Use Strict Mode** | Add `set -euo pipefail` for safer scripts |
| **Quote Variables** | Use `"$variable"` to prevent word splitting |
| **Use Functions** | Organize code into reusable functions |
| **Add Comments** | Document what each section does |
| **Handle Errors** | Include error handling and meaningful exit codes |
| **Validate Input** | Check arguments before processing |

```bash
#!/bin/bash
set -euo pipefail

# Good practice: validate input
if [ $# -lt 1 ]; then
    echo "Usage: $0 <filename>"
    exit 1
fi

# Good practice: quote variables
filename="$1"
if [ -f "$filename" ]; then
    echo "Processing $filename"
fi
```

### Security

| Practice | Description |
|----------|-------------|
| **Least Privilege** | Grant minimum necessary permissions |
| **SSH Keys Over Passwords** | Use SSH key authentication |
| **Disable Root SSH** | Prevent direct root login via SSH |
| **Keep Systems Updated** | Regularly apply security patches |
| **Use Firewalls** | Configure iptables/firewalld properly |
| **Audit Logs** | Regularly review system logs |
| **Strong Passwords** | Use complex passwords or passphrases |

```bash
# Disable root SSH login
sudo sed -i 's/PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config

# Set restrictive file permissions
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 700 ~/.ssh
```

### Virtualization

| Practice | Description |
|----------|-------------|
| **Snapshot Before Changes** | Create snapshots before major modifications |
| **Resource Planning** | Don't over-allocate CPU/RAM |
| **Use Templates** | Create VM templates for consistent deployments |
| **Network Isolation** | Use appropriate network modes for security |
| **Regular Backups** | Backup VM configurations and data |
| **Document Configurations** | Keep records of VM settings |

### Networking

| Practice | Description |
|----------|-------------|
| **Static IPs for Servers** | Use DHCP for clients, static for servers |
| **Document IP Addresses** | Maintain an IP address inventory |
| **Use Descriptive Hostnames** | Name servers by function (web01, db01) |
| **Secure Protocols** | Use SSH instead of Telnet, HTTPS over HTTP |
| **Firewall Rules** | Default deny, explicitly allow needed traffic |
| **Test Connectivity** | Verify network changes with ping, traceroute |

```bash
# Test connectivity before and after changes
ping -c 3 8.8.8.8
traceroute google.com
ss -tlnp  # Check listening ports
```

### Documentation

| Practice | Description |
|----------|-------------|
| **README Files** | Include setup instructions in every project |
| **Inline Comments** | Explain complex logic in code |
| **Change Logs** | Document what changed and why |
| **Screenshots** | Visual documentation for UI-based tasks |
| **Runbooks** | Step-by-step guides for common operations |
| **Keep Updated** | Update docs when procedures change |

---

## 🤝 Contributing

Contributions are welcome! If you find any issues or want to improve the content:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## 📜 License

This project is for educational purposes as part of the EPAM Cloud & DevOps Academy.

---

## 📞 Contact

For questions or feedback, please open an issue in this repository.

---

<div align="center">

**Happy Learning! 🎓**

*"The only way to learn DevOps is by doing DevOps"*

</div>

