# WMI

Modify ACLs to allow non-admin users access to securable objects

{% embed url="https://github.com/samratashok/RACE" %}

```
# On local machine for user
Set-RemoteWMI -SamAccountName student572 -Verbose

# On remote machine for user without explicit credentials
Set-RemoteWMI -SamAccountName student572 -ComputerName dcorp-dc -Namespace "root\cimv2" -Verbose

# On remote machine with explicit credentials. Only root\cimv2 and nested namespaces
Set-RemoteWMI -SamAccountName student572 -ComputerName dcorp-dc -Credential Administrator -Namespace "root\cimv2" -Verbose

# Remove permission on remote machine
Set-RemoteWMI -SamAccountName student572 -ComputerName dcorp-dc -Namespace "root\cimv2" -Remove -Verbose
```
