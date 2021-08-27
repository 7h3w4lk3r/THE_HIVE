# Loggon Sessions and Access Tokens

## Logon sessions and access tokens

The key concept to grasp in order to understand authentication in Windows environments is the relationship between **logon sessions** and **access tokens**. A logon session is used to represent the _presence_ of a user on a machine and begins when a user is successfully authenticated and ends when the user logs off.

  
For example, when a user physically logs on to a Windows workstation \(i.e., interactively\), they supply a username and password, which is then checked by the [Local Security Authority](https://docs.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication) \(LSA\). If the account is a local account \(i.e., only valid on that specific computer\) the LSA will check the credentials against its own security database. In the case of a Windows Active Directory domain environment, the authentication attempt is referred to the closest domain controller \(DC\) which will process the request and authenticate the user.  
  


![](../../../.gitbook/assets/image%20%2861%29.png)

 Once the user has been successfully authenticated, the LSA will create a new logon session and produce an access token, as shown above. A logon session can have multiple access tokens associated with it, but an access token can only ever be linked to one logon session \(which is typically the successful logon attempt that generated it\). Windows has legitimate functionality which can be used to change the logon session \(and hence cached credentials\) that your current token is associated with.  
  
 Every new logon session is identifiable via a 64 bit locally unique identifier \(LUID\), referred to as the logon ID, and every access token must contain an Authentication Id \(or AuthId\) parameter that identifies the origin/linked logon session via this LUID. This is highlighted in the diagram below:  
  
 

![](../../../.gitbook/assets/image%20%2859%29.png)

  
  
 Every access token is linked to a sole logon session, which is identifiable via the AuthID parameter. The AuthID field contains a 64 bit LUID, or logon ID, which identifies the origin logon session.

you can check the access tokens of the currently logged in user with:

```text
whoami /all

USER INFORMATION
----------------

User Name             SID
===================== ============================================
desktop-rgfrdxl\cpolo S-1-5-21-3359511372-53430657-2078432294-1001


GROUP INFORMATION
-----------------

Group Name                                                    Type             SID                                                                                                           Attributes
============================================================= ================ ============================================================================================================= ==================================================
Mandatory Label\Medium Mandatory Level                        Label            S-1-16-8192
Everyone                                                      Well-known group S-1-1-0                                                                                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account and member of Administrators group Well-known group S-1-5-114                                                                                                     Group used for deny only
BUILTIN\Administrators                                        Alias            S-1-5-32-544                                                                                                  Group used for deny only
BUILTIN\Users                                                 Alias            S-1-5-32-545                                                                                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Performance Log Users                                 Alias            S-1-5-32-559                                                                                                  Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\INTERACTIVE                                      Well-known group S-1-5-4                                                                                                       Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                                                 Well-known group S-1-2-1                                                                                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users                              Well-known group S-1-5-11                                                                                                      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization                                Well-known group S-1-5-15                                                                                                      Mandatory group, Enabled by default, Enabled group
MicrosoftAccount\cpolop@outlook.com                           User             S-1-11-96-3623454863-58364-18864-2661722203-1597581903-3158937479-2778085403-3651782251-2842230462-2314292098 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account                                    Well-known group S-1-5-113                                                                                                     Mandatory group, Enabled by default, Enabled group
LOCAL                                                         Well-known group S-1-2-0                                                                                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Cloud Account Authentication                     Well-known group S-1-5-64-36                                                                                                   Mandatory group, Enabled by default, Enabled group


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled
```

 or using _Process Explorer_ from Sysinternals \(select process and access"Security" tab\)  
  
The main function of an access token is to act as a “volatile repository for security settings associated with the logon session” which can be adjusted and modified on the fly. In this sense, access tokens act as a **proxy** or **stand-in** for the logon session and so when making security decisions, Windows developers never interact with the logon session itself \(which is “hidden” away in lsass\), but with an access token which **represents** it \(and hence predominantly via the Windows access token API\).   
Therefore, a developer can copy existing tokens \([DuplicateTokenEx](https://docs.microsoft.com/en-gb/windows/win32/api/securitybaseapi/nf-securitybaseapi-duplicatetokenex?redirectedfrom=MSDN)\), modify the security settings for a given token \([Get](https://docs.microsoft.com/en-us/windows/win32/api/securitybaseapi/nf-securitybaseapi-gettokeninformation)/[SetTokenInformation](https://docs.microsoft.com/en-us/windows/win32/api/securitybaseapi/nf-securitybaseapi-settokeninformation)\) etc. to their heart's content, but these tokens are still just abstractions representing the security settings from the _originating_ logon session.  
Most importantly, the access token represents the **security context** of the user. The security context can be defined as the privileges and permissions that a user has on a specific workstation \(and across the network\). An access token caches a number of [attributes](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens) which determine its security context, such as:  


• The security identifier \(SID\) for the user  
• Group memberships  
• Privileges held  
• A logon ID which _references_ the **origin** logon session  
  
For example, the screenshot below shows the cached security attributes for an access token using James Forshaw’s [TokenViewer](https://github.com/googleprojectzero/sandbox-attacksurface-analysis-tools):  


![](../../../.gitbook/assets/image%20%2857%29.png)

  
  
As discussed previously, the Authentication ID parameter, which is the key link between an access token and the logon session that it represents, contains a 64 bit LUID \(logon ID\) which identifies the origin logon session that this access token is associated with. Note also that it is possible to infer a number of other conclusions about the state of this token, e.g., it is a primary token, it is not elevated \(medium integrity\), and the user is an administrator \([Elevation Type](https://docs.microsoft.com/en-us/windows/win32/api/winnt/ne-winnt-token_elevation_type) = limited means the token is a ‘filtered’ admin token and hence UAC is enabled\).  
  
Whenever a thread [attempts to access a securable object](https://docs.microsoft.com/en-us/windows/win32/secauthz/interaction-between-threads-and-securable-objects) managed by the Windows kernel, such as a process, thread, handle, semaphore, token, etc., Windows will perform an [access check](https://docs.microsoft.com/en-us/windows/win32/api/securitybaseapi/nf-securitybaseapi-accesscheck). To perform this check, Windows needs three pieces of information2:

  
• **Who** is requesting access?  
• **What** are their intentions with the object?  
• **Who** can access the object?  
  
Hence, Windows will first check the token associated with the calling thread and look at the authorization attributes cached in it \(e.g., user sid, group memberships, privileges etc.\). Secondly, Windows will look at the desired [access](https://docs.microsoft.com/en-gb/windows/win32/secauthz/access-rights-and-access-masks) requested by the thread. In the Windows security model you **must** state your intentions upfront; for performance reasons an access check only occurs _once_ and no further checks are performed on any additional handle operations \(unless a user attempts to perform an action that the handle did not have rights to, e.g., write to a read-only handle\). Thirdly, Windows will retrieve the [security descriptor](https://docs.microsoft.com/en-us/windows/win32/secauthz/security-descriptors) for the target object. The security descriptor contains a discretionary access control list \([DACL](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-control-lists)\) which specifies what users/groups have access to the object and the _type_ of access granted.   
Based on these three sources of information, Windows can give a boolean answer to whether a principal has access to a given object. This is why every process **must** have a primary token; it is the user that is “charged” for any objects that process attempts to access.

  
As a note, some [privileges](https://docs.microsoft.com/en-us/windows/win32/secauthz/privilege-constants) can be thought of as simply enabling a user to bypass/skip the access check in the kernel for a given object. For example, if a token has the [SeDebugPrivilege](https://docs.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-openprocess) privilege enabled, the Windows kernel will skip the DACL checks for any process and thread objects \(hence why it is so powerful\).

  
Following successful authentication from an interactive logon, Windows will execute the user’s shell \(normally explorer.exe\) on behalf of the newly logged-on user. The operating system performs this action by using the newly minted access token to spawn explorer.exe as that user via [CreateProcessAsUserA](https://docs.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-createprocessasusera). This function takes a handle to a token and spawns a new process as the user specified in the token \(i.e., in a _different_ security context\).

  
Typically, every process created by the user is a child of the shell process \(i.e., explorer.exe\) ****and every new process will \(by default\) run in the **same** security context as its parent; hence the child process will inherit its parent’s access token upon creation. Therefore, _all_ processes will inherit their _own_ local copy of an access token.

  
As stated previously, access tokens act as a local “volatile repository” for the security settings associated with the logon session. As each process has its _own_ local copy of an access token, a process can modify the volatile security settings stored in its copy without affecting other processes. 

When a local administrator logins, **two access tokens are created**: One with admin rights and other one with normal rights. **By default**, when this user executes a process the one with **regular** \(non-administrator\) **rights is used**. When this user tries to **execute** anything **as administrator** \("Run as Administrator" for example\) the **UAC** will be used to ask for permission.

If you have **valid credentials of any other user**, you can **create** a **new logon session** with those credentials:

```text
runas /user:domain\username cmd.exe
```

The **access token** has also a **reference** of the logon sessions inside the **LSASS**, this is useful if the process needs to access some objects of the network. You can launch a process that **uses different credentials for accessing network services** using:

```text
runas /user:domain\username /netonly cmd.exe
```

This is useful if you have useful credentials to access objects in the network but those credentials aren't valid inside the current host as they are only going to be used in the network \(in the current host your current user privileges will be used\).

