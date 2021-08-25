# Privilege Escalation

#### Privilege escalation is a really big topic to cover and there are countless methods to achieve root/system level privileges. but im trying to focus on the most important techniques and start from the very basics.

## know before you start

**1. first rule of privilege escalation is that you HAVE TO be familiar with target system command line, actually the more comfortable you are with the shell the better and easier will be the privesc phase.**

**2. DO NOT jump to kernel exploits FIRST. yes, yes, in a CTF its a low-hanging fruit but in a real life scenario you dont want to crash the system SPECIALLY web and file servers, do you?**

**3. whatever tool you use for prives or whats called the post-exploitation information gathering, remember you should use manual techniques too. make sure you didnt miss anything.**

**4. all privilege escalations are effectively examples of access control violations. so if you can access what you want, there is usually no need to gain a root shell, except in a CTF or when you're trying to impress your client.**

## Checklist

here is a Linux privilege escalation checklist from [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md) that is very useful for a structured approach.

* Kernel and distribution release details
* System Information:

  * Hostname
  * Networking details:
  * Current IP
  * Default route details
  * DNS server information

* User Information:

  * Current user details
  * Last logged on users
  * Shows users logged onto the host
  * List all users including uid/gid information
  * List root accounts
  * Extracts password policies and hash storage method information
  * Checks umask value
  * Checks if password hashes are stored in /etc/passwd
  * Extract full details for 'default' uid's such as 0, 1000, 1001 etc
  * Attempt to read restricted files i.e. /etc/shadow
  * List current users history files \(i.e .bash\_history, .nano\_history, .mysql\_history , etc.\)
  * Basic SSH checks

* Privileged access:

  * Which users have recently used sudo
  * Determine if /etc/sudoers is accessible
  * Determine if the current user has Sudo access without a password
  * Are known 'good' breakout binaries available via Sudo \(i.e. nmap, vim etc.\)
  * Is root's home directory accessible
  * List permissions for /home/

* Environmental:

  * Display current $PATH
  * Displays env information

* Jobs/Tasks:

  * List all cron jobs
  * Locate all world-writable cron jobs
  * Locate cron jobs owned by other users of the system
  * List the active and inactive systemd timers

* Services:

  * List network connections \(TCP & UDP\)
  * List running processes
  * Lookup and list process binaries and associated permissions
  * List inetd.conf/xined.conf contents and associated binary file permissions
  * List init.d binary permissions

* Version Information \(of the following\):
  * Sudo
  * MYSQL
  * Postgres
  * Apache

    * Checks user config
    * Shows enabled modules
    * Checks for htpasswd files
    * View www directories
* Default/Weak Credentials:

  * Checks for default/weak Postgres accounts
  * Checks for default/weak MYSQL accounts

* Searches:

  * Locate all SUID/GUID files
  * Locate all world-writable SUID/GUID files
  * Locate all SUID/GUID files owned by root
  * Locate 'interesting' SUID/GUID files \(i.e. nmap, vim etc\)
  * Locate files with POSIX capabilities
  * List all world-writable files
  * Find/list all accessible \*.plan files and display contents
  * Find/list all accessible \*.rhosts files and display contents
  * Show NFS server details
  * Locate \*.conf and \*.log files containing keyword supplied at script runtime
  * List all \*.conf files located in /etc
  * Locate mail

* Platform/software specific tests:
  * Checks to determine if we're in a Docker container
  * Checks to see if the host has Docker installed
  * Checks to determine if we're in an LXC container



## Resources

{% embed url="https://book.hacktricks.xyz/linux-unix/privilege-escalation\#apparmor" %}

{% embed url="https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/" %}

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md" %}

{% embed url="https://sushant747.gitbooks.io/total-oscp-guide/privilege\_escalation\_-\_linux.html" %}

{% embed url="https://resources.infosecinstitute.com/topic/privilege-escalation-linux-live-examples/" %}

\*\*\*\*

## Methods

1. **cron/crontab abuse**
2. **/etc/passwd & /etc/shadow**
3. **MySQL UDF**
4. **sudo abuse**
5. **Environment variables**
6. **SUID/SGID binaries**
7. **Shared object injection**
8. **NFS**
9. **docker**
10. **Unix socket exploitation**
11. **and much more...**



## Automated Tools

a list of automated tools used in post-exploitation enumeration and privilege escalation.

{% embed url="https://github.com/AlessandroZ/BeRoot" %}

{% embed url="https://github.com/rebootuser/LinEnum" %}

{% embed url="https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS" %}

{% embed url="https://github.com/sleventyeleven/linuxprivchecker/blob/master/linuxprivchecker.py" %}

{% embed url="https://github.com/WazeHell/PE-Linux" %}

{% embed url="https://github.com/diego-treitos/linux-smart-enumeration" %}

{% embed url="https://github.com/mzet-/linux-exploit-suggester" %}

{% embed url="https://github.com/pentestmonkey/unix-privesc-check" %}

