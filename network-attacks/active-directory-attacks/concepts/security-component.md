# Security Component

## Intro

in this section we discuss the basic security concepts and components of windows security including authentication, authorization and access control

{% hint style="info" %}
Windows security components are way more complicated than Linux and any other OS  for that matter, partly because of the fact that windows was a single-user OS from the beginning but then turned into a multi-user OS after gaining more popularity. the other reason is probably Active Directory, which is the core of all windows-based networks and has its own security architecture and principles.
{% endhint %}

#### generally speaking, windows security is divided into 4 parts:

• [Type safety and security](https://docs.microsoft.com/en-us/dotnet/standard/security/key-security-concepts#type-safety-and-security)\
• [Principal](https://docs.microsoft.com/en-us/dotnet/standard/security/key-security-concepts#principal)\
• [Authentication](https://docs.microsoft.com/en-us/dotnet/standard/security/key-security-concepts#authentication)\
• [Authorization](https://docs.microsoft.com/en-us/dotnet/standard/security/key-security-concepts#authorization)\


for each of these topics the best reference is  [Microsoft official documentation page](https://docs.microsoft.com/en-us/windows-server/security/security-and-assurance) and for more details about windows system security and windows internals the main references are the windows internals books:

{% embed url="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjZiPm5_dDyAhVOJzQIHejqDEAQFnoECDQQAQ&url=https%3A%2F%2Fwww.amazon.com%2FWindows-Internals-Part-architecture-management%2Fdp%2F0735684189&usg=AOvVaw30BYywwn65DZTiKlQ0WtTe" %}

{% embed url="https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjZiPm5_dDyAhVOJzQIHejqDEAQFnoECB0QAQ&url=https%3A%2F%2Fwww.amazon.com%2FWindows-Internals-Part-2-7th%2Fdp%2F0135462401&usg=AOvVaw359iGH77ea7teGXebXANir" %}

i highly recommend reading the **official documents** and **windows internals part1 chapter 7** on system security but if you don't have the time or effort you can just stick with the introduction in this section.

## Security System Components

#### These are the core components and databases that implement Windows security:

### Security Reference Monitor (SRM)

A component in the Windows executive (%SystemRoot%\System32\Ntoskrnl.exe) that is responsible for defining the access token data structure to represent a security context, performing security access checks on objects, manipulating privileges (user rights), and generating any resulting security audit messages.



### **Local Security Authority subsystem (LSASS)**&#x20;

A user-mode process running the image %SystemRoot%\System32\Lsass.exe that is responsible for the local system security policy (such as which users are allowed to log on to the machine, password policies, privileges granted to users and groups, and the system security auditing settings), user authentication, and sending security audit messages to the Event Log. The Local Security Authority service (Lsasrv—%SystemRoot%\System32\Lsasrv.dll), a library that LSASS loads, implements most of this functionality.



### &#x20;**LSASS policy database**&#x20;

A database that contains the local system security policy settings. This database is stored in the registry in an ACL-protected area under HKLM\SECURITY. It includes such information as what domains are entrusted to authenticate logon attempts, who has permission to access the system and how (interactive, network, and service logons), who is assigned which privileges, and what kind of security auditing is to be performed. The LSASS policy database also stores “secrets” that include logon information used for cached domain logons and Windows service user-account logons. (See Chapter 4, “Management Mechanisms,” for more information on Windows services.)



### **Security Accounts Manager (SAM)**&#x20;

A service responsible for managing the database that contains the user names and groups defined on the local machine. The SAM service, which is implemented as %SystemRoot%\System32\Samsrv.dll, is loaded into the LSASS process.



### **SAM database**

&#x20;A database that contains the defined local users and groups, along with their passwords and other attributes. On domain controllers, the SAM does not store the domain-defined users, but stores the system’s administrator recovery account definition and password. This database is stored in the registry under HKLM\SAM.



### **Active Directory**&#x20;

A directory service that contains a database that stores information about objects in a domain. A _domain_ is a collection of computers and their associated security groups that are managed as a single entity. Active Directory stores information about the objects in the domain, including users, groups, and computers. Password information and privileges for domain users and groups are stored in Active Directory, which is replicated across the computers that are designated as domain controllers of the domain. The Active Directory server, implemented as %SystemRoot%\System32\Ntdsa.dll, runs in the LSASS process.&#x20;



### **Authentication packages**&#x20;

These include dynamic-link libraries (DLLs) that run both in the context of the LSASS process and client processes, and implement Windows authentication policy. An authentication DLL is responsible for authenticating a user, by checking whether a given user name and password match, and if so, returning to the LSASS information detailing the user’s security identity, which LSASS uses to generate a token.



### **Interactive logon manager (Winlogon)**

&#x20;A user-mode process running %SystemRoot%\System32\Winlogon.exe that is responsible for responding to the SAS and for managing interactive logon sessions. Winlogon creates a user’s first process when the user logs on, for example.



### **Logon user interface (LogonUI)**

&#x20;A user-mode process running %SystemRoot%\System32\LogonUI.exe that presents users with the user interface they can use to authenticate themselves on the system. LogonUI uses credential providers to query user credentials through various methods.\


### **Credential providers (CPs)**&#x20;

In-process COM objects that run in the LogonUI process (started on demand by Winlogon when the SAS is performed) and used to obtain a user’s name and password, smartcard PIN, or biometric data (such as a fingerprint). The standard CPs are %SystemRoot%\System32\authui.dll and %SystemRoot%\System32\SmartcardCredentialProvider.dll.\


### **Network logon service (Netlogon)**&#x20;

A Windows service (%SystemRoot%\System32\Netlogon.dll) that sets up the secure channel to a domain controller, over which security requests—such as an interactive logon (if the domain controller is running Windows NT 4) or LAN Manager and NT LAN Manager (v1 and v2) authentication validation—are sent. Netlogon is also used for Active Directory logons.\


### **Kernel Security Device Driver (KSecDD)**&#x20;

A kernel-mode library of functions that implement the advanced local procedure call (ALPC) interfaces that other kernel mode security components, including the Encrypting File System (EFS), use to communicate with LSASS in user mode. KSecDD is located in %SystemRoot%\System32\Drivers\Ksecdd.sys.\


### Security Support Provider Interface (SSPI)

Is the API that can be use to authenticate users. The SSPI will be in charge of finding the adequate protocol for two machines that want to communicate. The preferred method for this is Kerberos. Then the SSPI will negotiate which authentication protocol will be used, these authentication protocols are called Security Support Provider (SSP), are located inside each Windows machine in the form of a DLL and both machines must support the same to be able to communicate.

#### Main SSPs:

*   **Kerberos:** The preferred one %windir%\Windows\System32\kerberos.dll

    ****
*   **NTLMv1 and NTLMv2:** Compatibility reasons %windir%\Windows\System32\msv1\_0.dll

    ****
*   **Digest:** Web servers and LDAP, password in form of a MD5 hash %windir%\Windows\System32\Wdigest.dll The SSPI will be in charge of finding the adequate protocol for two

    ****
*   **Schannel:** SSL and TLS %windir%\Windows\System32\Schannel.dll

    ****
* **Negotiate:** It is used to negotiate the protocol to use (Kerberos or NTLM being Kerberos the default one) %windir%\Windows\System32\lsasrv.dll



### **AppLocker**

&#x20;A mechanism that allows administrators to specify which executable files, DLLs, and scripts can be used by specified users and groups. AppLocker consists of a driver (%SystemRoot%\System32\Drivers\AppId.sys) and a service (%SystemRoot%\System32\AppIdSvc.dll) running in a SvcHost process.\


#### the relationships among some of these components and the databases they manage **are shown in picture bellow:**

![](<../../../.gitbook/assets/image (55).png>)

\


