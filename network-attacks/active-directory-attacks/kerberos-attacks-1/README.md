# Persistence

## Kerberos Authentication Steps

![](<../../../.gitbook/assets/image (181).png>)

## Kerberos Keys

#### kerberos has three long-term keys:

**client long-term secret key (derived from the client account password) **based on the password of the client account, used to check encrypted timestamp (AS-REQ) and encrypt session key (AS_REP)

**target(service) long-term secret key (derived from the service account password)**. based on the password of target service, used to encrypt service portion of the ST (TGT-REP) and sign the PAC (TGT-REP)

**KDC long-term secret key (derived from the krbtgt account password). **based on the infamous the krbtgt accounts password hash. used to encrypt the TGT (AS-REP) and sign the PAC (AS-REP and TGS-REP)

{% hint style="info" %}
all of these keys are based on certain accounts
{% endhint %}

be default kerberos will use the highest method of encryption that is supported by the client. for microsoft based systems as of windows vista all machines support AES encryption types. but while kerberosting we will request for RC4 encryption which i easier to crack.
