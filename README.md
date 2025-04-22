# Ansible Server Setup with Ubuntu, 2 nodes.
This guide walks you through setting up an Ansible server and configuring two Ubuntu 20.04 LTS client machines (node1, node2) to be managed using Ansible. It includes instructions for installing required packages, configuring SSH access, and testing the Ansible environment.

---

## Infrastructure Setup

Create **three Ubuntu 20.04 LTS virtual machines**:
- `server` – Ansible server
- `node1` – Ansible client
- `node2` – Ansible client

---

## Install Required Packages

### On Ansible Server Only

```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible rpm tree vim -y
```

### On node1 and node2

```bash
sudo apt update
sudo apt install sshpass tree rpm vim -y
```

---

## SSH Configuration for Cloud VMs

Ensure SSH access is properly configured.


### Azure:

```bash
sudo vim /etc/ssh/sshd_config
```

Ensure the following lines are present:

```
PasswordAuthentication yes
KbdInteractiveAuthentication yes
```

Restart the SSH service:

```bash
sudo service ssh restart
```

---

## Create `ansible` User on All Machines

```bash
sudo adduser ansible
```
Use the password: `......`

---

##  Grant Passwordless Sudo to `ansible` User

Edit sudoers file:

```bash
sudo visudo
```

Add:

```
ansible ALL=(ALL:ALL) NOPASSWD: ALL
```

Save with `Ctrl + O`, then exit with `Ctrl + X`.

---

## Configure SSH Key-Based Authentication

### On Ansible Server (as `ansible` user)

Generate SSH key:

```bash
sudo su - ansible
ssh-keygen
```

Copy SSH key to client machines:

```bash
ssh-copy-id node1
ssh-copy-id node2
```

Enter password `ranga12345` when prompted.

Test SSH access:

```bash
ssh node1
ssh node2
```

---

## Verify Ansible Installation

```bash
ansible --version
```

---

## Setup Environment on Ansible Server

Create environment directory and configuration:

```bash
mkdir dev
cd dev
```

Create `ansible.cfg`:

```ini
[defaults]
inventory=hosts
remote_user=ansible
timeout=3000

[privilege_escalation]
become=True
become_method=sudo
become_user=root
become_ask_pass=False
```

Create `hosts` file for static inventory:

```ini
[dev1]
node1

[dev2]
node2
```

---

## Test the Ansible Setup

List hosts:

```bash
ansible all --list
```

Expected Output:

```
hosts (2):
    node1
    node2
```

Ping test:

```bash
ansible all -m ping
ansible node1 -m ping
```

Expected Output: **green pong responses**

---

## Conclusion

I have successfully set up an Ansible server with two Ubuntu nodes! now I can now manage configurations, deploy applications, and automate tasks across your environment using Ansible.


