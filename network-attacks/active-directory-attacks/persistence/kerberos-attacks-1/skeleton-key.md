# Skeleton Key

Skeleton key can unlock all systems in the domain, it only works for kerberos RC4 encryption and is a backdoor that runs on the domain controller \(in memory \) and allows a single password \(the skeleton password\) that can be used to log into any account.

skeleton key does this by manipulating the way the encrypted timestamp \(AS-REQ\) is validated. in RC4 the timestamp is encrypted using NT hash of the user by the client, after 

