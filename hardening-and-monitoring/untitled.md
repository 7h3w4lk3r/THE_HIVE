---
description: >-
  Since this is not a complete Linux tutorial documentation I'm not going to
  explain every little detail of Linux OS architecture, just some tips for
  hardening your OS.
---

# Linux Security Hardening

In the following sections i will talk about the most important aspects of Linux system and server security hardening stuff that you should be familiar with and here is a overall checklist of these topics that might come in handy.



## OS Security

| **Topic** | **Methods and Tools** |
| :--- | :--- |
| **Update strategy** | Applying security updates automation and package validation |
| **SELinux** | Configuring access control systems, labeling and type enforcement in SELinux |
| **Grub security** | Bootloader password protection for preventing physical attacks |
| **AppArmor** | Pretty much like SELinux but with different operation modes |
| **Kernel hardening** | Configuring systemctl and setting kernel parameters with Lynis |
| **Process isolation** | Control groups, namespace isolation and resource limitations |

## Account Security

| **Topic** | **Methods and Tools** |
| :--- | :--- |
| **Account aging** | Setup account aging and expiration date |
| **Account access control** | Configuring root access, lock/unlock accounts, account shell settings |
| **Sudoers configurations** | Managing sudo privileges and custom rules |

## Filesystem Security

| **Topic** | **Methods and Tools** |
| :--- | :--- |
| **Ownership and access** | Managing files and directories ownership and access control |
| **ACLs and umasks** | Setup access control lists and umasks |

## Firewalls

| **Topics** | **Methods and Tools** |
| :--- | :--- |
| **Default packet filtering** | Basic setup of default Linux firewalls like iptables and ufw |
| **IPFire** | Linux distribution based free firewall with built-in IPS |
| **pfSense** | Stateful packet filtering free firewall |

## Logging and Log Analysis

| **Topic** | **Methods and Tools** |
| :--- | :--- |
| **journald** | Setup local or remote logging with journald  |
| **ELK** | A combination of Elasticsearch, Logstash and Kibana for logging and analysis |



