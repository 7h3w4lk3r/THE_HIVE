# Skeleton Key

#### Skeleton key can unlock all systems in the domain, it only works for kerberos RC4 encryption and is a backdoor that runs on the domain controller \(in memory \) and allows a single password \(the skeleton password\) that can be used to log into any account.

skeleton key does this by manipulating the way the encrypted timestamp \(AS-REQ\) is validated. in RC4 the timestamp is encrypted using NT hash of the user by the client, after which the domain controller attempts to decrypt the timestamp using the user NT hash. when the skeleton key is installed the domain controller will attempt to decrypt the timestamp using the user s NT hash and the skeleton key NT hash \(which is 'mimikatz' by default for mimikatz tool\).

#### to deploy the skeleton key run mimikatz with DC privileges and run this command:

```text
mimikatz# misc::skeleton
```

![](../../../../.gitbook/assets/image%20%28229%29.png)

#### now anyone with the skeleton key password "mimikatz" can authenticate to any user account.

