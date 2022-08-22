# SSH Hijacking

The term SSH hijacking refers to the <mark style="color:green;">**use of an existing SSH connection to gain access to another machine.**</mark> Two of the most common methods of SSH hijacking use the `ControlMaster` feature or the `ssh-agent`.

## <mark style="color:red;">Crack Private Key</mark>

If we find some user's password-protected private SSH key, we can attempt to crack it with JTR.

first convert the key to a crackable format:

```
python /usr/share/john/ssh2john.py private.key > private.hash
```

run JTR with a wordlist:

```
sudo john --wordlist=/usr/share/wordlists/rockyou.txt ./private.hash
```

after successfully cracking the key, use ssh with -i option to login to the remote system with that private key and then specify the cracked password.

```
ssh -i private.key victim@target
```

## &#x20;<mark style="color:red;">ControlMaster</mark>

### <mark style="color:orange;">Non-root Scenario</mark>

ControlMaster is a feature that enables sharing of multiple SSH sessions over a single network connection. <mark style="color:green;">**This functionality can be enabled for a given user by editing their local SSH configuration file (\~/.ssh/config).**</mark>

This file can be created or modified by users with elevated privileges or write access to the user’s home folder. By doing so, we can create an attack vector when there wasn’t one originally, by enabling ControlMaster functionality for an unwitting user.

create the `~/.ssh/config` file:

```
Host *
    ControlPath ~/.ssh/target/%r@%h:%p
    ControlMaster auto
    ControlPersist 10m
```

* <mark style="color:green;">host \*</mark> --> this config is for all hosts
* <mark style="color:green;">ControlPath</mark> --> path for target system socket file with the name `<remoteusername@<targethost>:<port>`  . this assumes that the `target` folder already exists.
* <mark style="color:green;">ControlMaster</mark> --> identifies that any new connections will attempt to use existing ControlMaster sockets when possible. When those are unavailable, it will start a new connection.
* <mark style="color:green;">ControlPersist -</mark>-> can either be set to “yes” or to a specified time. If it is set to “yes”, the socket stays open indefinitely. Alternatively, it will accept new connections for a specified amount of time after the last connection has terminated. In the above configuration, the socket will remain open for 10 minutes after the last connection and then it will close.

{% hint style="info" %}
These ControlMaster settings can also be placed in `/etc/ssh/ssh_config` to configure ControlMaster at a system-wide level.
{% endhint %}

set the correct permission on the config file:

```
chmod 644 ~/.ssh/config
```

create the `target` folder:

```
mkdir ~/.ssh/target
```

after a user has established a legitimate SSH session from the compromised machine, a socket file will be created in the `target` folder.&#x20;

```
ls -al ~/.ssh/target/
```

This socket file represents the legitimate SSH session to the downstream server.&#x20;

now, if we simply SSH to the server listed in the victim’s socket file, we will not be prompted for a password and are given direct access to the target machine via SSH.

### <mark style="color:orange;">root Scenario</mark>

In the this scenario, we’re logged in as a root user (or someone with sudo privileges). <mark style="color:green;">**In this case, we can use the -S switch to use the socket created for any compromised user on that system.**</mark>

```
ssh -S /home/user1/.ssh/target/user2\@target_ip\:22 user2@target_ip
```

## <mark style="color:red;">SSH-Agent and SSH Agent Forwarding</mark>

### <mark style="color:orange;">Non-root Scenario</mark>

SSH-Agent is a utility that keeps track of a user’s private keys and allows them to be used without having to repeat their passphrases on every connection.

SSH agent forwarding is a mechanism that <mark style="color:green;">**allows a user to use the SSH-Agent on an intermediate server as if it were their own local agent on their originating machine.**</mark> This is useful in situations where a user might need to ssh from an intermediate host into another network segment, which can’t be directly accessed from the originating machine. It has the advantage of not requiring the private key to be stored on the intermediate server and the user does not need to enter their passphrase more than once.

<mark style="color:green;">**This works by passing the SSH key response requests from the remote destination servers back through the SSH-Agent on the intermediate hosts to the originating client’s SSH Agent for key validation.**</mark>

To use an SSH-Agent, there needs to be an SSH keypair set up on the originating machine.

For our SSH connections to work using SSH-Agent forwarding, we need to have our public key installed on both the intermediate server and the destination server.

We can copy our key to both of them using the ssh-copy-id command from attacking machine:

{% hint style="danger" %}
#### remember to set a passphrase for the key, otherwise, the ssh agent cant keep track of the connections.
{% endhint %}

```
ssh-copy-id -i ~/.ssh/id_rsa.pub user1@compromised
ssh-copy-id -i ~/.ssh/id_rsa.pub user2@target
```

we need to set our local SSH config file in \~/.ssh/config on our attacking machine to have the following line:

```
ForwardAgent yes
```

This tells the SSH client we’re connecting from to enable agent forwarding for connections.

Next, on the intermediate server,we need to have the following line set in `/etc/ssh/sshd_config`.

```
AllowAgentForwarding yes
```

This allows the intermediate server to forward key challenges back to the originating client’s SSH agent.

SSH-Agent is automatically set to run on many Linux distributions, to start it manually on our attack machine:

```
eval `ssh-agent`
```

We can now add our keys to the SSH-Agent on our attack machine using ssh-add.If we just want to use the key that is in the default key location (\~/.ssh/id\_rsa), we don’t need to specify any parameters. Alternatively, we can add the path to the key file we want to use immediately after the command. In our case, since our key is in the default location, we can just run ssh-add.

```
ssh-add
```

Now that our key is registered with the agent, all we need to do to connect to the downstream server is a pair of ssh commands. We’ll first ssh to the intermediate and then from there to the target host.

```
ssh user1@compromised
ssh user2@target
```

{% hint style="info" %}
#### we’ll need to type our private key passphrase for the first connection so that the SSH- Agent can keep track of it.
{% endhint %}

we have compromised the account of a user who is logged in to the intermediate server. since the intermediate system acts as if we already have the user’s SSH keys available, we can SSH to any downstream server the compromised user’s private key has access to.

{% hint style="info" %}
#### To exploit this, the compromised user needs to have an active SSH connection to the intermediate server.
{% endhint %}

### <mark style="color:orange;">root Scenario</mark>

The SSH-Agent mechanism creates an open socket file on the intermediate server that can be accessed by users with elevated permissions. If we’ve compromised an account with root level access on the intermediate server, we can leverage the victim user’s open socket directly.

To perform this:

First determine the SSH process ID:

```
ps aux | grep sshd
pstree -p user1 | grep ssh
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
