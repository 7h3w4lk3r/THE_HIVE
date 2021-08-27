# Network Authentication

## Network authentication

  
 Having covered local authentication and access control, what happens under the hood when a user needs to access some resource located across the network? For example, a user could attempt to view the available shares on another host by running the following command:  


![](../../../.gitbook/assets/image%20%2846%29.png)

The user’s logon session is unique to their workstation \(as is their access token and privileges\) and they cannot simply send their access token over the wire. The token would be meaningless as it does not correspond to a valid logon session on the remote host. Furthermore, this authentication mechanism would be an obvious target for replay attacks.

In this case, the user needs to re-authenticate and establish a new logon session on the remote machine \(assuming the user has access\). For an interactive logon \(and actually all other logon types like service, batch, etc., except network\) Windows will automatically cache the credentials as part of the Windows single sign-on \(SSO\) mechanism. This is the intended design of the Windows SSO mechanism and prevents the user from having to constantly re-enter their password when accessing network resources.

As a consequence, access tokens which link back to these types of logon sessions can authenticate to remote hosts and Windows will automatically authenticate on the users behalf whenever a network resource is accessed by a thread or process. Note that Windows will always use the credentials cached in the logon session that the access token is linked to when authenticating remotely \(e.g., Windows will find the token’s linked logon session, via the AuthId, and use the credentials cached for that logon session, as shown below\).

![](../../../.gitbook/assets/image%20%2851%29.png)

Therefore, in order to establish a new logon session, the SMB server will need to authenticate the client over the network. In Windows domains, network authentication is typically performed via [Kerberos](https://docs.microsoft.com/en-us/windows-server/security/kerberos/kerberos-authentication-overview) or the legacy challenge-response protocol [NTLM](https://docs.microsoft.com/en-us/windows/win32/secauthn/microsoft-ntlm). Irrespective of the network authentication protocol used, on receiving an authentication request the target host will forward the credential information to the [DC](https://docs.microsoft.com/en-us/windows/win32/secauthn/key-distribution-center) and, following successful authentication, establish a new _network_ login session for the user \(i.e., **this login** “**represents a remote client”**\).

  
Network logins **do not** cache credentials and therefore you cannot use this token to authenticate to another remote host. This is commonly referred to as the ‘double hop’ problem. Note that due to the inherent [design](https://docs.microsoft.com/en-us/windows/win32/secauthn/microsoft-ntlm) of the NTLM challenge response protocol \(e.g., the client encrypts a challenge with the user’s NTLM hash\) it fundamentally does not support credential delegation.  
Most importantly from the server’s perspective, following the successful authentication of the remote user, it is presented with a newly minted **access token** which represents the network logon of the remote client. The diagram below illustrates this process:

![](../../../.gitbook/assets/image%20%2848%29.png)

This neatly leads to the second key concept for Windows access tokens: [impersonation](https://docs.microsoft.com/en-us/windows/win32/com/impersonation). As previously mentioned, access tokens encode a wealth of information about the security context of the user and enable a handy way for developers to make “localized” changes to this context _without_ affecting other processes. However, in **multi-threaded** applications, problems and difficult-to-debug race conditions may arise if different threads start enabling/disabling different privileges or modifying default token DACLs.

  
As a result, Windows has a feature called impersonation. By default _all_  threads will inherit the same security context as their process’s primary token. However, impersonation allows a thread to switch to a _different_ security context. Specifically, it enables threads to have their own local copy of a token; known as an impersonation token. This is the best way to remember the distinction between primary and impersonation tokens, in that impersonation tokens are always applied to threads, whereas primary tokens are associated with processes.In this way, the SMB server can handle each incoming client request in a separate thread and impersonate the access token representing the remote client.

  
Also note that switching security context has two implications. The first is that **locally** the thread is now impersonating a different access token, and hence any **local** access checks will be performed using this new token. Secondly, as this impersonated token may be linked to a **different** logon session \(and therefore potentially have _different_ cached credentials \(if a non-network login\)\) the thread’s security context **remotely** is also _different_. Furthermore, as we shall see in the next blog post in this series, do not always assume that the cached credentials stored in lsass **match** the user specified in the token.  
In summary, from the perspective of a listening server process \(say an SMB file server\), the following steps must occur following a connection request from a remote client:

  
• The user is authenticated and a new logon session is created \(**NETWORK\_ONLY**\)  
• The server process is presented with a handle to an impersonation token which links back to the remote client’s new **network logon session**  
• The server can use this token to impersonate the client to perform work on their behalf  
  
This approach has the added benefit of making use of the existing Windows’ access control model, as all actions performed while impersonating are under the security context of the user’s identity \(hence any local access check decisions will use the information cached in the impersonated user’s token\). Therefore, if that user does not already have access to a specific file on a share, they will be denied access.

  
For most of Windows’ key IPC mechanisms \(e.g., named pipes, RPC, COM\) this process is handled automatically. The server needs only to call the appropriate API in order to obtain a handle to the remote clients’ security context \(i.e., access token\) and start impersonating the client via functions such as:

  
 [ImpersonateLoggedOnUser](https://docs.microsoft.com/en-us/windows/win32/api/securitybaseapi/nf-securitybaseapi-impersonateloggedonuser)  
 [CoImpersonateClient](https://docs.microsoft.com/en-us/windows/win32/api/combaseapi/nf-combaseapi-coimpersonateclient)  
[RpcImpersonateClient](https://docs.microsoft.com/en-us/windows/win32/api/rpcdce/nf-rpcdce-rpcimpersonateclient)  
[ImpersonateNamedPipeClient](https://docs.microsoft.com/en-us/windows/win32/api/namedpipeapi/nf-namedpipeapi-impersonatenamedpipeclient)  
 [DdeImpersonateClient](https://docs.microsoft.com/en-gb/windows/win32/api/ddeml/nf-ddeml-ddeimpersonateclient)  




