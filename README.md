# 🛡️ Maintenance Playbook

This repository contains an **Ansible automation setup** for maintaining and securing **RHEL** and **Ubuntu** systems across multiple environments:
**DEV**, **STAGE**, **PROD**,

It automates system hardening, agent deployment, SELinux/AppArmor configuration, journald tuning, and cache hygiene — ensuring consistent and secure baselines across environments.


## 🔑 Prerequisites

Before running any roles:

### 1. Install Ansible (Ubuntu) Example
```
sudo su - root
apt install -y software-properties-common
add-apt-repository --yes --update ppa:ansible/ansible
apt install -y ansible
ansible --version
```

### 2. Create an SSH Key (if you don’t already have one)

```
ssh-keygen -t rsa -b 4096 -C "youremail@example.com"
```

### 3. Copy your SSH Key to Target Hosts.

```
ssh-copy-id -i ~/.ssh/id_ed25519.pub root@IP    # Ubuntu test host
ssh-copy-id -i ~/.ssh/id_ed25519.pub root@IP   # RHEL test host
```

### 4. Verify SSH Accesss
```
ssh root@IP -p 22
```
You should connect without being prompted for a password.


### 5. Test a role
Run SELinux role:
```
ansible-playbook -i hosts.ini setup.yml -e target=UAT --tags selinux --limit testvm-rhel
```

Run OS settings role:
```
ansible-playbook -i hosts.ini setup.yml -e target=UAT --tags os_settings --limit testvm-rhel
```

Run SentinelOne & Rapid7 install role:
```
ansible-playbook -i hosts.ini setup.yml -e target=UAT --tags s1_r7_install --limit testvm-ubuntu
```

Kernel cleanup and upgrade
```
ansible-playbook -i hosts.ini setup.yml -e target=UAT --tags kernel_cleanup_and_upgrade --limit testvm-rhel
```

### Parameters:
Option	Description
```
-i hosts.ini	Specify the inventory file
-e target=<ENV>	Select environment group (e.g., UAT, PROD, CFG)
--tags <TAG>	Run specific role only
--limit <HOST>	Limit execution to one or more specific hosts
```
---
