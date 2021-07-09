---
description: >-
  Since this is not a complete Linux tutorial I'm not going to explain every
  little detail of Linux OS architecture and administration, just some tips for
  hardening your OS.
---

# Linux Security Hardening

In the following sections i will talk about the most important aspects of Linux system and server security hardening stuff that you should be familiar with and here is a list of these topics that might come in handy.

### OS Security

| **Topic** | **Methods and Tools** |
| :--- | :--- |
| **Update strategy** | Applying security updates automation and package validation |
| **SELinux** | Configuring access control systems, labeling and type enforcement in SELinux |
| **Grub security** | Bootloader password protection for preventing physical attacks |
| **AppArmor** | Pretty much like SELinux but with different operation modes |
| **Kernel hardening** | Configuring systemctl and setting kernel parameters with Lynis |
| **Process isolation** | Control groups, namespace isolation and resource limitations |

### Account Security

| **Topic** | **Methods and Tools** |
| :--- | :--- |
| **Account aging** | Setup account aging and expiration date |
| **Account access control** | Configuring root access, lock/unlock accounts, account shell settings |
| **Sudoers configurations** | Managing sudo privileges and custom rules |

### Filesystem Security

| **Topic** | **Methods and Tools** |
| :--- | :--- |
| **Ownership and access** | Managing files and directories ownership and access control |
| **ACLs and umasks** | Setup access control lists and umasks |

### Firewalls

| **Topics** | **Methods and Tools** |
| :--- | :--- |
| **Default packet filtering** | Basic setup of default Linux firewalls like iptables and ufw |
| **IPFire** | Linux distribution based free firewall with built-in IPS |
| **pfSense** | Stateful packet filtering free firewall |

### Logging and Log Analysis

| **Topic** | **Methods and Tools** |
| :--- | :--- |
| **journald** | Setup local or remote logging with journald  |
| **ELK** | A combination of Elasticsearch, Logstash and Kibana for logging and analysis |



Since there are so many checklists, standards and guides for operating system hardening out there, i tried to make this as short as possible so i wont need to read through 200 pages of some manual every time i want to configure a system.



## My Hardening Checklist 

####  This checklist is a combination of my own experience plus the methods and techniques included in guides like CIS benchmarks and some other resources on the web. the target in most of these items is Ubuntu/Debian based distros but most of them are applicable on RedHat/CentOS as well. this is by no means an all-in-one solution and i highly appreciate any comments, adages and rants you might have :\)















 

