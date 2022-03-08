# PowerShell Remoting

PSRemoting allows you to run commands on remote computers just as if you were sitting in front of them. PSRemoting provides a set of features that connects and authenticates a user user, runs remote commands and returns any output from that command to the local computer.

Think of PSRemoting like telnet or SSH or even psexec. It’s just a way to run commands on computers within PowerShell.

enable powershell remoting:

{% embed url="https://github.com/samratashok/RACE" %}

```
# On local machine
Set-RemotePSRemoting -SamAccountName student572 -Verbose

# On remote machine without credentials
Set-RemotePSRemoting -SamAccountName student572 -ComputerName dcorp-dc -Verbose

# Remove permission on remote machine
Set-RemotePSRemoting -SamAccountName student572 -ComputerName dcorp-dc -Remove -Verbose

```
