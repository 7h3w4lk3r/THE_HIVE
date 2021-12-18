# 🔧 Credential Dumping

## Registry

The registry is a central component that contains necessary boot and system information, system-wide software settings, the security database and also per-user configuration settings. The registry is composed of keys and values. A key is a container that can consist of keys and values. The highest level of keys is called root keys. The registry contains the following six root keys:

![](<../../../.gitbook/assets/image (254).png>)

For each service, a registry key exists in HKLM\SYSTEM\CurrentControlSet\Services. The subkeys of a service’s key contain information regarding the executable file path, parameters and configuration options. If a service is configured to start from a particular account’s security context, the subkeys also contain the username.

Services run in the context of user accounts. Unless otherwise specified, a service runs under the local system account (referred to as SYSTEM or LocalSystem), which possesses the highest privileges.

### LSA Secrets

#### LSA Secrets is a registry location which contains important data that are used by the Local Security Authority like authentication, logging users on to the host, local security policy etc. This information is stored in the following registry key.

```
HKEY_LOCAL_MACHINE/Security/Policy/Secrets
```

Due to the sensitivity of information Windows is protecting access to the Security folder in the registry with permissions. By default only the SYSTEM account can access the LSA Secrets registry location This location contains the password of the account that is logged in an encrypted format. However the key to reverse the password is stored in the parent key: Policy.

![](<../../../.gitbook/assets/image (250).png>)

Registry keys of interest are except of Security, the SAM and the System as they contain password hashes. From an elevated command prompt the registry keys can be saved with the reg utility.

## Local Security Authority Subsystem Service (LSASS)

#### LSASS is responsible for authoritative domain authentication, active directory management, and enforcing security policies. It generates the processes accountable for authenticating users with NTLM as well as verifies the validity of logins. Because it's so crucial to the functionality of the operating system, hackers will often rename malicious executables after the process.

The Windows 10 Task Manager can also be used to dump LSASS memory, without the help of Mimikatz or ProcDump. Mousejack payload designed to extract and exfiltrate the LSASS dump with only keystroke injection exploits and PowerShell. The attack is completed in under ten seconds .he Task Manager is opened in the Run window with administrative privileges. The screen turns entirely dark for a second due to the User Access Control (UAC) prompt, which prevents the GIF creator from recording the screen. Then, the Local Security Authority Process (lsass.exe) is located in the list of processes and dumped into the %TEMP% directory (by default). A PowerShell one-liner is then executed entirely from the run window. It compresses the LSASS dump into a ZIP file and sends it to the attacker's server.

## windows Credentials Protections

### WDigest

WDigest protocol was introduced in Windows XP and was designed to be used with HTTP Protocol for authentication. Microsoft has this protocol enabled by default in multiple versions of Windows (Windows XP — Windows 8.0 and Windows Server 2003 — Windows Server 2012) which means that plain-text passwords are stored in the LSASS (Local Security Authority Subsystem Service).



### LSA Protection

Microsoft in Windows 8.1 and later has provided additional protection for the LSA to prevent untrusted processes from being able to read its memory or to inject code. This will prevent regular mimikatz.exe sekurlsa:logonpasswords for working properly. To activate this protection you need to set the value RunAsPPL in HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Control\LSA to 1.



### Credential Guard

Credential Guard is a new feature in Windows 10 (Enterprise and Education edition) that helps to protect your credentials on a machine from threats such as pass the hash. This works through a technology called Virtual Secure Mode (VSM) which utilizes virtualization extensions of the CPU (but is not an actual virtual machine) to provide protection to areas of memory (you may hear this referred to as Virtualization Based Security or VBS). VSM creates a separate "bubble" for key processes that are isolated from the regular operating system processes, even the kernel and only specific trusted processes may communicate to the processes (known as trustlets) in VSM. This means a process in the main OS cannot read the memory from VSM, even kernel processes. The Local Security Authority (LSA) is one of the trustlets in VSM in addition to the standard LSASS process that still runs in the main OS to ensure support with existing processes but is really just acting as a proxy or stub to communicate with the version in VSM ensuring actual credentials run on the version in VSM and are therefore protected from attack. Credential Guard must be turned on and deployed in your organization as it is not enabled by default.



### RDP RestrictedAdmin Mode

With Windows 8.1 and Windows Server 2012 R2, new security features were introduced. One of those security features is the Restricted Admin mode for RDP. This new security feature is introduced to mitigate the risk of pass the hash attacks. When you connect to a remote computer using RDP, your credentials are stored on the remote computer that you RDP into. Usually you are using a powerful account to connect to remote servers, and having your credentials stored on all these computers is a security threat indeed. Using Restricted Admin mode for RDP, when you connect to a remote computer using the command, mstsc.exe /RestrictedAdmin, you will be authenticated to the remote computer, but your credentials will not be stored on that remote computer, as they would have been in the past. This means that if a malware or even a malicious user is active on that remote server, your credentials will not be available on that remote desktop server for the malware to attack. Note that as your credentials are not being saved on the RDP session if try to access network resources your credentials won't be used. The machine identity will be used instead.

### Cached Credentials

Domain credentials are used by operating system components and are authenticated by the Local Security Authority (LSA). Typically, domain credentials are established for a user when a registered security package authenticates the user's logon data. This registered security package may be the Kerberos protocol or NTLM.

Windows stores the last ten domain login credentials in the event that the domain controller goes offline. If the domain controller goes offline, a user will still be able to log into their computer. This feature is mainly for laptop users that do not regularly log into their company’s domain. The number of credentials that the computer stores can be controlled by the following registry key, or via group policy:

```
HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\WINDOWS NT CURRENTVERSION\WINLOGON\CACHEDLOGONSCOUNT
```

The credentials are hidden from normal users, even administrator accounts. The SYSTEM user is the only user that has privileges to view these credentials. In order for an administrator to view these credentials in the registry they must access the registry as a SYSTEM user. The Cached credentials are stored in the registry at the following registry location:

```
HKEY_LOCAL_MACHINE\SECURITY\Cache
```

### Protected Users

When the signed in user is a member of the Protected Users group the following protections are applied:

Credential delegation (CredSSP) will not cache the user's plain text credentials even when the Allow delegating default credentials Group Policy setting is enabled.

Beginning with Windows 8.1 and Windows Server 2012 R2, Windows Digest will not cache the user's plain text credentials even when Windows Digest is enabled.

NTLM will not cache the user's plain text credentials or NT one-way function (NTOWF).

Kerberos will no longer create DES or RC4 keys. Also it will not cache the user's plain text credentials or long-term keys after the initial TGT is acquired.

A cached verifier is not created at sign-in or unlock, so offline sign-in is no longer supported.

After the user account is added to the Protected Users group, protection will begin when the user signs in to the device.

## Windows Hash Types



Below is a short list of the most useful hash types for Active Directory hunting.

| Hash type                            | -m number for hashcat |
| ------------------------------------ | --------------------- |
| LM hash                              | 3000                  |
| NT hash                              | 1000                  |
| ​LM response​                        | ​not supported​       |
| ​LMv2 response​                      | ​not supported​       |
| ​NTLM response​                      | 5500                  |
| ​NTLMv2 response​                    | 5600                  |
| ​(DCC1) Domain Cached Credentials​   | 1100                  |
| ​(DCC2) Domain Cached Credentials 2​ | 2100                  |
| ​ASREProast​                         | 18200                 |
| ​Kerberoast​                         | 13100                 |
