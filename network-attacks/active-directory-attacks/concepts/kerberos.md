# Kerberos

## <mark style="color:red;">Windows hashes</mark>

There are a few different types of hashes in Windows and they can be very confusing. Some explanations can be found [here](http://www.adshotgyan.com/2012/02/lm-hash-and-nt-hash.html) and [here](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4) but read this first:

No Windows hashes are salted, so two identical hashes will yield the same plaintext. Windows hashes are broken down into two hashes. LMhash and NTLMhash. This is an example: `testuser:29418:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71`

Broken down:\
`username : unique_identifier : LMhash : NThash`

* **LM** - The LM hash is used for storing passwords. It is disabled in W7 and above. However, LM is enabled in memory if the password is less than 15 characters. That's why all recommendations for admin accounts are 15+ chars. LM is old, based on MD4 and easy to crack. The reason is that Windows domains require speed, but that also makes for shit security.
* **NT** -  The NT hash calculates the hash based on the entire password the user entered. The LM hash splits the password into two 7-character chunks, padding as necessary.
* **NTLM** - The NTLM hash is used for local authentication on hosts in the domain. It is a combination of the LM and NT hash as seen above.
* **NetNTLMv1/2** - Hash for authentication on the network (SMB). Sometimes called NTLMv2, but don't get confused; it is not the same as an NTLM hash.

In windows the hashes are stored in memory for single sign-on purposes. Everytime a user clicks on a network share the creds are passed across the network. We can exploit this by grabbing those credentials while in transit or on the machine itself. The alternative is to always ask the user for credentials, which will rarely happen in a windows environment.

NTLM hash is just as good as plaintext creds when authenticating to windows machines so it's not that big of a deal if you can't grab plaintext credentials. By good I mean it is possible to just pass the hash to authenticated, you don't need the password itself.

Cracking hashes can be a lot of fun, and since most user passwords are shitty / not complex they can easily be cracked in the manner of seconds or minutes. Imagine if the domain has a password reset policy of 90 days. If you crack a user's credentials in two hours it's a big fail for them. If you can crack a domain admin's creds in two hours or even a few days it's game over for them. But instead of cracking hashes, we can reuse them by relaying.

## <mark style="color:red;">Authentication in Windows</mark>

There are numerous ways of proving identity in Windows systems.

* **Passwords** - Passwords are the basic and default way of authenticating users.
* **Hashes** - Windows can use hashes for authentication. It is possible to leverage attacks like pass-the-hash to prove identity with a compromised user, completely without the account password.
* **Tokens** - the concept of token is identity.  When a user or service logs in to a system, the system validates their identity once, and mints a token, which is handed to that user/service and serves as their identity. The system then doesn't need to validate identity every time a program opens a file, for example. This basically ensures a clean separation between authentication (proving a user/service is who they say they are) and authorization (determining whether a user/service can access some resource).
* **Tickets** - usually refers to Kerberos tickets, see below.

## <mark style="color:red;">Kerberos</mark>

**Kerberos** provides a centralized authentication server whose function is to authenticate users to servers and servers to users. In Kerberos Authentication server and database is used for client authentication. Kerberos runs as a third-party trusted server known as the Key Distribution Center (KDC). Each user and service on the network is a principal.

Kerberos is composed of three parts: a client, a server, and a trusted third party known as the Kerberos Key Distribution Center (KDC). The KDC provides authentication and ticket granting services.

The KDC maintains a database or repository of user accounts for all of the security principals in its realm. Many Kerberos distributions use file-based repositories for the Kerberos principal and policy DB and others use Lightweight Directory Access Protocol (LDAP) as the repository.

Kerberos does not support any notion of groups (that is, iKeys groups or groups of users or principals). The KDC maintains a long-term key for each principal in its accounts database. This long-term key is derived from the password of the principal. Only the KDC and the user that the principal represents should know what the long-term key or password is.

Each party in a Kerberos environment authenticates to the Kerberos server and receives a ticket . More precisely, this is a ticket - granting - ticket: a ticket that can be used to request tickets . When one party (client) wants to use a service from a second party ( server ), the first party uses its ticket - granting - ticket to obtain a ticket for the second party from the Kerberos server and then presents it to the second party, thereby authenticating to the third party . Since both parties trust the Kerberos server , a ticket provided by the Kerberos server is trusted by both parties , leading to authentication and authorization .

{% hint style="info" %}
Before Kerberos was introduced, pre-Active Directory Windows domains (Windows NT) relied on NTLM challenge/response authentication .

NTLM provides authentication between two parties without a third trusted party .

**If Kerberos cannot be used (various reasons apply), Windows will fall back to NTLM authentication .**
{% endhint %}

## <mark style="color:red;">Kerberos authentication</mark>

![](<../../../.gitbook/assets/image (301) (1).png>)

Firstly, Kerberos is an authentication protocol, not authorization. In other words, it allows to identify each user, who provides a secret password, however, it does not validates to which resources or services can this user access.

Kerberos is used in Active Directory. In this platform, Kerberos provides information about the privileges of each user, but it is responsability of each service to determine if the user has access to its resources.

## <mark style="color:red;"></mark>

* The **TGS** (Ticket Granting Service) is the ticket which user can use to authenticate against a service. It is encrypted with the service key.
* The **TGT** (Ticket Granting Ticket) is the ticket presented to the KDC to request for TGSs. It is encrypted with the KDC key.

### <mark style="color:orange;">**PAC**</mark>

The **PAC** (Privilege Attribute Certificate) is an structure included in almost every ticket. This structure contains the privileges of the user and it is signed with the KDC key.

It is possible to services to verify the PAC by comunicating with the KDC, although this does not happens often. Nevertheless, the PAC verification consists of checking only its signature, without inspecting if privileges inside of PAC are correct.

Furthermore, a client can avoid the inclusion of the PAC inside the ticket by specifying it in _KERB-PA-PAC-REQUEST_ field of ticket request.

### <mark style="color:orange;">**Messages**</mark>

Kerberos uses differents kinds of messages. The most interesting are the following:

* <mark style="color:green;">**KRB\_AS\_REQ**</mark><mark style="color:green;">:</mark> Used to request the TGT to KDC.
* <mark style="color:green;">**KRB\_AS\_REP**</mark><mark style="color:green;">:</mark> Used to deliver the TGT by KDC.
* <mark style="color:green;">**KRB\_TGS\_REQ**</mark><mark style="color:green;">:</mark> Used to request the TGS to KDC, using the TGT.
* <mark style="color:green;">**KRB\_TGS\_REP**</mark><mark style="color:green;">:</mark> Used to deliver the TGS by KDC.
* <mark style="color:green;">**KRB\_AP\_REQ**</mark><mark style="color:green;">:</mark> Used to authenticate a user against a service, using the TGS.
* <mark style="color:green;">**KRB\_AP\_REP**</mark><mark style="color:green;">:</mark> (Optional) Used by service to identify itself against the user.
* <mark style="color:green;">**KRB\_ERROR**</mark><mark style="color:green;">:</mark> Message to comunicate error conditions.

Additionally, even if it is not part of Kerberos, but NRPC, the AP optionally could use the **KERB\_VERIFY\_PAC\_REQUEST** message to send to KDC the signature of PAC, and verify if it is correct.

Below is shown a summary of message sequency to perform authentication:

![](https://www.tarlogic.com/wp-content/uploads/2019/01/kerberos\_message\_summary.png)

## <mark style="color:red;">Authentication process</mark>

In this section, the sequency of messages to perform authentication will be studied, starting from a user without tickets, up to being authenticated against the desired service.

### <mark style="color:orange;">**KRB\_AS\_REQ**</mark>

Firstly, user must get a TGT from KDC. To achieve this, a KRB\_AS\_REQ must be sent:

![](https://www.tarlogic.com/wp-content/uploads/2019/03/KRB\_AS\_REQ.png)

_KRB\_AS\_REQ_ has, among others, the following fields:

* A encrypted **timestamp** with client key, to authenticate user and prevent replay attacks
* **Username** of authenticated user
* The service **SPN** asociated with **krbtgt** account
* A **Nonce** generated by the user

Note: the encrypted timestamp is only necessary if user requires preauthentication, which is common, except if [_DONT\_REQ\_PREAUTH_](https://support.microsoft.com/en-us/help/305144/how-to-use-the-useraccountcontrol-flags-to-manipulate-user-account-pro) \_\_ flag is set in user account.

### <mark style="color:orange;">**KRB\_AS\_REP**</mark>

After receiving the request, the KDC verifies the user identity by decrypting the timestamp. If the message is correct, then it must respond with a _KRB\_AS\_REP_:

![](https://www.tarlogic.com/wp-content/uploads/2019/02/KRB\_AS\_REP.png)

_KRB\_AS\_REP_ includes the next information:

* **Username**
* **TGT**, which includes:
  * **Username**
  * **Session key**
  * **Expiration date** of TGT
  * **PAC** with user privileges, signed by KDC
* Some **encrypted data** with user key, which includes:
  * **Session key**
  * **Expiration date** of TGT
  * User **nonce**, to prevent replay attacks

Once finished, user already has the TGT, which can be used to request TGSs, and afterwards access to the services.

### <mark style="color:orange;">**KRB\_TGS\_REQ**</mark>

In order to request a TGS, a _KRB\_TGS\_REQ_ message must be sent to KDC:

![](https://www.tarlogic.com/wp-content/uploads/2019/02/KRB\_TGS\_REQ-1.png)

_KRB\_TGS\_REQ_ includes:

* **Encrypted data** with session key:
  * **Username**
  * **Timestamp**
* **TGT**
* **SPN** of requested service
* **Nonce** generated by user

### <mark style="color:orange;">**KRB\_TGS\_REP**</mark>

After receiving the _KRB\_TGS\_REQ_ message, the KDC returns a TGS inside of _KRB\_TGS\_REP_:

![](https://www.tarlogic.com/wp-content/uploads/2019/02/KRB\_TGS\_REP.png)

_KRB\_TGS\_REP_ includes:

* **Username**
* **TGS**, which contains:
  * **Service session key**
  * **Username**
  * **Expiration date** of TGS
  * **PAC** with user privileges, signed by KDC
* **Encrypted data** with session key:
  * **Service session key**
  * **Expiration date** of TGS
  * User **nonce**, to prevent replay attacks

### <mark style="color:orange;">**KRB\_AP\_REQ**</mark>

To finish, if everything went well, the user already has a valid TGS to interact with service. In order to use it, user must send to the AP a _KRB\_AP\_REQ_ message:

![](https://www.tarlogic.com/wp-content/uploads/2019/02/KRB\_AP\_REQ.png)

_KRB\_AP\_REQ_ includes:

* **TGS**
* **Encrypted data** with service session key:
  * **Username**
  * **Timestamp**, to avoid replay attacks

After that, if user privileges are rigth, this can access to service. If is the case, which not usually happens, the AP will verify the PAC against the KDC. And also, if mutual authentication is needed it will respond to user with a _KRB\_AP\_REP_ message.

## <mark style="color:red;">Kerberos Security Vulnerabilities</mark>

### <mark style="color:orange;">Golden Ticket Attack</mark> <a href="#golden-ticket-attack" id="golden-ticket-attack"></a>

A golden ticket is a forged Kerberos key distribution center. You can create usable Kerberos tickets for accounts that do not exist in the Active Directory.

To obtain a Golden ticket, an attacker needs domain/local administrator access on Active Directory forest or domain – and once the ticket is created, it is good for 10 years by default!

If you believe that someone created an unauthorized golden ticket, you would need to reset the Kerberos service account, krbtgt. While this isn't difficult, there are several critical steps to the process.

Because Active Directory stores the old and current passwords for all accounts, you must reset the krbtgt account twice. But the second reset should occur **only after waiting the maximum user ticket lifetime** after the first password reset.

### <mark style="color:orange;">Silver Ticket Attack</mark> <a href="#silver-ticket-attack" id="silver-ticket-attack"></a>

A silver ticket is similar to a Golden Ticket, but does not have the broad administrative privileges of the golden ticket.

An attacker would typically only gain access to a single service on an application, and an attacker must have compromised legitimate user credentials from a computer's SAM or local service account.

What makes these attacks very difficult to detect is that forging a silver ticket (for example using the service account password hash) does not require any communication with a DC.

### <mark style="color:orange;">Backdoor skeleton key malware attack</mark> <a href="#backdoor-skeleton-key-malware-attack" id="backdoor-skeleton-key-malware-attack"></a>

In a backdoor skeleton key malware attack, the attacker typically has compromised the Domain Controller and executed a successful Golden Ticket attack.

The malware injects into LSASS a master password that would work against any account in the domain. When the account authenticates, the malware will check the injected master password hash, and if it's a match will authenticate the user, regardless of the user's true password. Legitimate users will still be able to log in with their normal credentials.

### <mark style="color:orange;">Pass the hash attack</mark> <a href="#pass-the-hash-attack" id="pass-the-hash-attack"></a>

This is a technique where an attacker obtains a user's NTLM password hash, and subsequently passes the hash through for NTLM authentication purposes.

This works because systems do not actually validate a user's password, but rather the hash of the password. This attack only works against interactive logons using NTLM authentication.

### <mark style="color:orange;">Pass the ticket</mark> <a href="#pass-the-ticket" id="pass-the-ticket"></a>

In this attack, the threat actor creates a fake session key by forging a fake TGT. The attacker will present this to the service as a valid credential.

In order to execute this attack, the attacker must obtain access to the session key. To perform this attack, an attacker would obtain Kerberos tickets from the memory of the LSASS process, and then inject the stolen TGT into their own session, which will let them adopt the identity and privileges of the stolen TGT.

### <mark style="color:orange;">Overpass the hash</mark> <a href="#overpass-the-hash" id="overpass-the-hash"></a>

A combination of Pass the hash and Pass the ticket, an attacker uses a compromised hash to obtain a Kerberos ticket that they can use to access a resource.

Often useful if you need Kerberos authentication if NTLM is disabled to reach your target but only have a compromised hash.

### <mark style="color:orange;">Kerberoasting</mark> <a href="#kerberoasting" id="kerberoasting"></a>

When a domain account is configured to run a service (for example, Internet Information Systems, MSSQL, and so on.), a Service Principal Name is used to associate the service with a login account.

If a user wants to access the resource, they receive a Kerberos ticket signed with the NTLM password hash of the account running the service. Hackers can then crack this hash offline and use it to gain access.

_Any user on the domain with a valid TGT can request a TGS for any service with an SPN_ - no fancy credentials or access needed! Note that _there is no fix or patch_ beyond ensuring that the password for the service accounts are sufficiently complex.

To detect this attack, your _only_ native option is to monitor for event ID 4769, and look for a Ticket Encryption Type of 0x17 - user to user krb\_tgt\_reply. You can find more information on detecting Kerberoast attacks [here](https://www.trustedsec.com/blog/art\_of\_kerberoast/).

### <mark style="color:orange;">Replay attack</mark> <a href="#replay-attack" id="replay-attack"></a>

A replay attack occurs if an attacker steals the packet sent from the user to the service, which they can then use to gain access to the service without knowing the user's credentials.

This is generally low risk and is mitigated by the system checking the timestamp of the packet - any timestamp earlier or the same as a previous packet is rejected, as well as any timestamp out of sync with the server time by over 5 minutes.
