# Ansible Q&A - Kubernetes Cluster Deployment

This project uses Ansible to deploy a Kubernetes cluster with K3s and AI/ML applications.

## Overview

This project includes:
- K3s cluster deployment (1 master node, 2 worker nodes)
- Configuration management through Ansible

## Directory Structure

`
ansible/
 hosts.ini                 # Inventory file
 site.yml                  # Main playbook
 group_vars/
    all.yml               # Global variables
 host_vars/
    master_node.yml       # Master node specific variables
    worker_node_1.yml     # Worker node 1 specific variables
    worker_node_ai.yml    # Worker node AI specific variables
 roles/
     k3s/                  # K3s installation and configuration
     k3s-manifests/        # Kubernetes manifests deployment
     k3s-prereq/          # Prerequisites installation
`

## System Requirements

### Control Node (Ansible host)
- Ubuntu 20.04+ or CentOS 8+
- Ansible ≥ 2.12 (recommended ≥ 2.14)
- SSH client

### Target Nodes
- Ubuntu 20.04+ or CentOS 8+
- SSH server
- Sudo privileges

## Installation Guide

### 1. Environment Preparation

#### 1.1 Install Ansible (on Control Node)

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Ansible
sudo apt install -y ansible

# Check version
ansible --version
```

### 2. SSH Passwordless Setup

#### 2.1 Create SSH key

```bash
ssh-keygen -t ed25519 -C "ansible@control-node"
```

#### 2.2 Copy SSH key to target nodes

```bash
ssh-copy-id user@<MASTER_IP>
ssh-copy-id user@<WORKER_NODE_1_IP>
ssh-copy-id user@<WORKER_NODE_AI_IP>
```

#### 2.3 Test SSH connection

```bash
ssh user@<MASTER_IP>
ssh user@<WORKER_NODE_1_IP>
ssh user@<WORKER_NODE_AI_IP>
```

#### 2.4 Setup Passwordless sudo

On each target node, execute:

```bash
ssh user@<SERVER_IP>
sudo visudo
```

Add the following line to the end of the file:
```
<username> ALL=(ALL) NOPASSWD: ALL
```

**Note:** Replace `<username>` with the actual username on each server.

### 3. Project Configuration

#### 3.1 Clone project

```bash
git clone <repository-url>
cd ansible-qna
```

#### 3.2 Update inventory

Edit the hosts.ini file:

```ini
[k3s_cluster:children]
master
worker

[master]
master_node ansible_host=<MASTER_IP>

[worker]
worker_node_1 ansible_host=<WORKER_NODE_1_IP>
worker_node_ai ansible_host=<WORKER_NODE_AI_IP>
```

#### 3.3 Update host variables

Update files in the `host_vars/` directory:
- master_node.yml
- worker_node_1.yml
- worker_node_ai.yml

Change the `ansible_user` value to match the username on each server.

### 4. Deployment

#### 4.1 Check connectivity

```bash
cd ansible
ansible all -i hosts.ini -m ping
```

#### 4.2 Run playbook

```bash
ansible-playbook -i hosts.ini site.yml
```

#### 4.3 Verify cluster

After successful deployment, check cluster status:

```bash
# On master node
kubectl get nodes
kubectl get pods -A
```

## Deployed Applications

The project will deploy the following components:
- **Frontend**: User interface
- **Backend**: API server
- **AI Model**: AI/ML model
- **Qdrant**: Vector database
- **SQL Server**: Relational database
- **Migration**: Database migration jobs

### Logs

Check Ansible logs:
```bash
ansible-playbook -i hosts.ini site.yml -v
```