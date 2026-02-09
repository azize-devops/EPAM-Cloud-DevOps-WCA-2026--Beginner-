# EPAM Cloud & DevOps Fundamentals - Beginner Track

<div align="center">

![DevOps](https://img.shields.io/badge/DevOps-Fundamentals-blue?style=for-the-badge&logo=dev.to&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-CentOS-yellow?style=for-the-badge&logo=linux&logoColor=white)
![Git](https://img.shields.io/badge/Git-Version%20Control-F05032?style=for-the-badge&logo=git&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-Scripting-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white)

**A comprehensive self-study guide for Cloud & DevOps fundamentals**

[Getting Started](#-getting-started) •
[Modules](#-course-modules) •
[Structure](#-repository-structure) •
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

| Status | Description |
|--------|-------------|
| 🚧 | *Coming Soon* |

---

## 📁 Repository Structure

```
EPAM-Cloud-DevOps-WCA-2026--Beginner-/
│
├── 📁 Hypervisor Essentials/
│   └── 📁 Virtualization Introduction/
│       ├── 📄 task.txt
│       └── 📄 answer.txt
│
├── 📁 Networking/
│   ├── 📁 Network Services/
│   └── 📁 Subnets and routing with protocols/
│
├── 📁 CentOS Linux/
│   ├── 📁 CentOS Linux Essentials/
│   │   ├── 📁 Basic Shell Usage/
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
│   └── 📁 Linux Networking Basics/
│
├── 📁 Version Control with GIT/
│   ├── 📁 I Can Win/
│   ├── 📁 Bring It On/
│   ├── 📁 Hurt Me Plenty/
│   ├── 📁 Hardcore/
│   └── 📁 Nightmare!/
│
├── 📁 Scripting-Bash/
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
| **Operating System** | CentOS 7/8, RHEL |
| **Version Control** | Git, GitHub |
| **Networking** | TCP/IP, DNS, DHCP, Firewall |
| **Shell** | Bash, Vi/Vim |
| **Services** | systemd, SSH, httpd, nginx |

</div>

---

## 📈 Learning Path

```
┌─────────────────────────────────────────────────────────────────┐
│                        BEGINNER TRACK                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Hypervisor    ──►    Networking    ──►    CentOS Linux        │
│   Essentials           Fundamentals         Essentials          │
│                                                                  │
│        │                    │                    │               │
│        ▼                    ▼                    ▼               │
│   ┌─────────┐        ┌───────────┐       ┌────────────┐         │
│   │ Virtual │        │  OSI/TCP  │       │   Shell    │         │
│   │ Machines│        │    IP     │       │  Basics    │         │
│   └─────────┘        └───────────┘       └────────────┘         │
│                                                 │                │
│                                                 ▼                │
│                                          ┌────────────┐         │
│                                          │  Advanced  │         │
│                                          │ Networking │         │
│                                          └────────────┘         │
│                                                 │                │
│        ┌────────────────────────────────────────┘                │
│        ▼                                                         │
│   ┌──────────┐         ┌─────────────┐                          │
│   │   Git    │   ──►   │   Bash      │                          │
│   │ Mastery  │         │  Scripting  │                          │
│   └──────────┘         └─────────────┘                          │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

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
