# 1. Linux Virtual Machine Setup Documentation

## Table of Contents
1. [Introduction](#introduction)
2. [Virtual Machine Setup](#virtual-machine-setup)
    - [1. Download and Install VirtualBox](#download-and-install-virtualbox)
    - [2. Create a New Virtual Machine](#create-a-new-virtual-machine)
    - [3. Install Ubuntu Server](#install-ubuntu-server)
3. [Linux System Configuration](#linux-system-configuration)
    - [1. Update and Upgrade](#update-and-upgrade)
    - [2. Network Configuration](#network-configuration)
    - [3. User Accounts](#user-accounts)
    - [4. Security Measures](#security-measures)
4. [Documentation](#documentation)
    - [1. Installation Steps](#installation-steps)
    - [2. Configuration Details](#configuration-details)
    - [3. Customizations and Best Practices](#customizations-and-best-practices)

---

## Introduction

This document provides a step-by-step guide for setting up a virtual machine with a Linux distribution (Ubuntu Server) using VirtualBox. It includes the installation process, basic Linux system configurations, and documentation of customizations and best practices implemented.

---

## Virtual Machine Setup

### 1. Download and Install VirtualBox

- Download the VirtualBox software from the official website: [https://www.virtualbox.org/](https://www.virtualbox.org/).
- Follow the installation instructions according to your operating system.

### 2. Create a New Virtual Machine

- Open VirtualBox and click on "New."
- Name the virtual machine (e.g., UbuntuServer).
- Choose "Linux" as the type and "Ubuntu (64-bit)" as the version.
- Allocate sufficient RAM (e.g., 2GB or more) and create a new virtual hard disk.

### 3. Install Ubuntu Server

- Download the Ubuntu Server ISO from [https://ubuntu.com/download/server](https://ubuntu.com/download/server).
- In VirtualBox, select the virtual machine and click on "Settings."
- Under "System," move the CD/DVD option to the top in the "Boot Order" tab.
- Go to "Storage," select the empty disk under "Controller: IDE," and choose the downloaded Ubuntu ISO as the optical disk file.
- Start the virtual machine; it will boot from the ISO.

## Linux System Configuration

### 1. Update and Upgrade

```bash
sudo apt update
sudo apt upgrade
```

### 2. Network Configuration

- Edit the network configuration file:

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

Example content for a static IP:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: no
      addresses: [192.168.1.2/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

Apply the changes:

```bash
sudo netplan apply
```

### 3. User Accounts

- Add a new user and grant sudo privileges:

```bash
sudo adduser yourusername
sudo usermod -aG sudo yourusername
```

### 4. Security Measures

- Install and configure a firewall (e.g., UFW):

```bash
sudo apt install ufw
sudo ufw allow OpenSSH
sudo ufw enable
```

- Disable root login via SSH:

Edit sshd_config file:

```bash
sudo nano /etc/ssh/sshd_config
```

Set `PermitRootLogin` to `no` and restart the SSH service:

```bash
sudo service ssh restart
```

- Regularly update the system:

```bash
sudo apt update && sudo apt upgrade
```

## Documentation

### 1. Installation Steps

- Document the step-by-step installation process, including screenshots if possible.

### 2. Configuration Details

- Detail the network configuration, user accounts created, and any security measures implemented.

### 3. Customizations and Best Practices

- Highlight any additional customizations made to enhance security or improve system performance.

This documentation serves as a reference for replicating the setup in the future or for others. Adjust the instructions based on specific requirements and best practices for your environment.

# 2. Mail Server and DNS Configuration Documentation

## Table of Contents
1. [Introduction](#introduction)
2. [Mail Server Installation and Configuration](#mail-server-installation-and-configuration)
    - [1. Install Postfix](#1-install-postfix)
    - [2. Configure Postfix](#2-configure-postfix)
    - [3. Test Mail Server](#3-test-mail-server)
3. [DNS Configuration](#dns-configuration)
    - [1. Configure DNS Records](#1-configure-dns-records)
    - [2. Test Domain Resolution](#2-test-domain-resolution)
4. [Documentation](#documentation)
    - [1. Mail Server Configuration](#1-mail-server-configuration)
    - [2. DNS Configuration](#2-dns-configuration)
    - [3. Troubleshooting Steps](#3-troubleshooting-steps)

---

## Introduction

This document outlines the steps for installing and configuring a mail server (Postfix) on a Linux system. Additionally, it covers the configuration of DNS records for domain resolution. The testing process for the mail server's functionality, including sending and receiving emails, is also included. Troubleshooting steps taken during the configuration process are documented for reference.

---

## Mail Server Installation and Configuration

### 1. Install Postfix

- Install Postfix using the package manager:

  ```bash
  sudo apt install postfix
  ```

### 2. Configure Postfix

- During the Postfix installation, the system will prompt you to choose a configuration. Select "Internet Site" and enter the fully qualified domain name (FQDN) when prompted.

- Edit the main.cf file to configure Postfix:

  ```bash
  sudo nano /etc/postfix/main.cf
  ```

  Example configuration for a basic setup:

  ```plaintext
  myhostname = mail.yourdomain.com
  mydomain = yourdomain.com
  myorigin = $mydomain
  inet_interfaces = all
  ```

- Restart Postfix to apply the changes:

  ```bash
  sudo systemctl restart postfix
  ```

### 3. Test Mail Server

- Test sending an email:

  ```bash
  echo "This is a test email." | mail -s "Test Subject" user@example.com
  ```

  Check the mail logs for any errors:

  ```bash
  sudo tail /var/log/mail.log
  ```

- Test receiving an email:

  Send an email to your configured email address and check the inbox.

---

## DNS Configuration

### 1. Configure DNS Records

- Access your domain registrar's control panel and add the following DNS records:

  - **MX Record:**
    - Type: MX
    - Priority: 10 (or your preferred priority)
    - Value: mail.yourdomain.com

  - **A Record:**
    - Type: A
    - Host: mail (or the desired subdomain)
    - Value: Your server's IP address

### 2. Test Domain Resolution

- Use a tool like `nslookup` to check if the DNS records are resolving correctly:

  ```bash
  nslookup mail.yourdomain.com
  ```

  Ensure that the IP address returned matches your server's IP.

---

## Documentation

### 1. Mail Server Configuration

- Document the steps taken to install and configure Postfix.
- Include the main.cf file configurations.
- Mention any issues encountered during the configuration and how they were resolved.

### 2. DNS Configuration

- Document the DNS records configured for the mail server.
- Include details such as MX and A records.
- Note any challenges faced during DNS configuration and how they were addressed.

### 3. Troubleshooting Steps

- Document any troubleshooting steps taken during the testing phase.
- Include error messages from mail logs and how issues were resolved.

This documentation serves as a comprehensive guide for setting up a mail server and configuring DNS. It provides a reference for future maintenance and troubleshooting. Adjust the instructions based on specific requirements and the chosen mail server software.

# 3. Ansible Automation Documentation

## Table of Contents
1. [Introduction](#introduction)
2. [Ansible Playbooks](#ansible-playbooks)
    - [1. Install and Configure Software Packages](#1-install-and-configure-software-packages)
    - [2. Automate Routine Maintenance Tasks](#2-automate-routine-maintenance-tasks)
3. [Execution and Testing](#execution-and-testing)
    - [1. Execute Ansible Playbooks](#1-execute-ansible-playbooks)
    - [2. Verify Successful Execution](#2-verify-successful-execution)
4. [Documentation](#documentation)
    - [1. Playbook Creation Process](#1-playbook-creation-process)
    - [2. Playbook Executions](#2-playbook-executions)

---

## Introduction

This document outlines the process of using Ansible to automate common tasks across Linux systems. Two Ansible playbooks have been created: one for installing and configuring common software packages (e.g., Apache web server, MySQL database), and another for automating routine maintenance tasks such as system updates and backups. The execution and testing of these playbooks are also documented.

---

## Ansible Playbooks

### 1. Install and Configure Software Packages

- Create a playbook, `install_software.yml`, to install and configure software packages:

  ```yaml
  ---
  - name: Install and Configure Software Packages
    hosts: your_server
    become: true

    tasks:
      - name: Update apt cache
        apt:
          update_cache: yes

      - name: Install Apache web server
        apt:
          name: apache2
          state: present

      - name: Install MySQL database server
        apt:
          name: mysql-server
          state: present
  ```

### 2. Automate Routine Maintenance Tasks

- Create a playbook, `routine_maintenance.yml`, to automate routine maintenance tasks:

  ```yaml
  ---
  - name: Automate Routine Maintenance Tasks
    hosts: your_server
    become: true

    tasks:
      - name: Update system packages
        apt:
          upgrade: yes

      - name: Perform system backup
        command: /path/to/backup_script.sh
  ```

---

## Execution and Testing

### 1. Execute Ansible Playbooks

- Run the playbooks using the `ansible-playbook` command:

  ```bash
  ansible-playbook -i your_inventory_file install_software.yml
  ansible-playbook -i your_inventory_file routine_maintenance.yml
  ```

  Replace `your_inventory_file` with the path to your Ansible inventory file.

### 2. Verify Successful Execution

- Check for any errors during playbook execution.

- Ensure that the software packages are installed and configured correctly.

- Confirm that routine maintenance tasks (system updates and backups) have been executed without errors.

---

## Documentation

### 1. Playbook Creation Process

- Document the process of creating each playbook.
- Include explanations for each task in the playbooks.
- Highlight any challenges faced during playbook creation and how they were resolved.

### 2. Playbook Executions

- Document the execution of each playbook.
- Include any command-line parameters or specific considerations.
- Provide output and error logs generated during playbook execution.

This documentation serves as a reference for automating common tasks using Ansible. Adjust the playbooks based on specific software versions and system configurations. Regularly update playbooks to accommodate changes in the environment or requirements.
