# 🟤 SSH Tricks

## <mark style="color:red;">**Run Single Command**</mark>

```
ssh -o StrictHostKeyChecking=no root@172.17.0.8 [command]
ssh -p 2222 username@hostname pwd

# SSH with verbose output 	
ssh -vvv -p 2702 root@45.33.87.74 date 2>&1
```

## <mark style="color:red;">**SSH**</mark> <mark style="color:red;">without input password</mark>

```
// Sapt install sshpass
sshpass -p '<your-passwd>' ssh <username>@<ssh_host> i
```

## <mark style="color:red;">Diff local file with remote one</mark>

```
/diff local_file.txt <(ssh <username>@<ssh_host> 'cat remote_file.txt')
```

## <mark style="color:red;">Diff two remote SSH files</mark>

```
// Sodiff <(ssh user@remote_host 'cat file1.txt') <(ssh user2@remote_host2 'cat file2.txt')
```

## <mark style="color:red;">SCP with timestamps/permissions kept</mark>

```
scp -rp /tmp/abc/ ec2-user@<ssh-host>:/root/
```

## <mark style="color:red;">Ansible</mark>

This is actually the basis of how some server provisioning tools work. In fact, Ansible is very similar - it will run commands over SSH on groups of servers (in series and in parallel).

Let's see how that works on Ubuntu really quickly.

First install Ansible on a server that will be doing provisioning (not the one being provisioned):

```
sudo apt-add-repository -y ppa:rquillo/ansible
sudo apt-get update
sudo apt-get install -y ansible
```

Then, configure one or more servers in the `/etc/ansible/hosts` directory:

```
[web]
192.168.22.10
192.168.22.11
192.168.22.12
```

Save that file and then let's run a command on all three servers!

```
$ ansible -k all -m ping -u vagrant
```

This will run "ping" on each server. You'll get some JSON output saying if they were successful or not.

The flags of that command:

* `-k` - Ask for password
* `all` - All servers configured in `/etc/ansible/hosts`
* `-m ping` - Use the ping module
* `-u vagrant` - Login with user "vagrant", which will work if the hosts defined are other vagrant servers. Change the username as needed.

You can actually run any command using the "shell" module:

```
ansible -k all -m shell -u vagrant -a "apt-get install nginx"
```

Here, the `-a "apt-get install nginx` will run the given command using the "shell" module.

## <mark style="color:red;">SSH control sequences</mark>

When in an SSH connection, press the tilde character (shift + backtick) followed by another character to use the control sequence options (shift + \~). to get help about commands press ? after \~.

#### Supported escape sequences:

```
    ~. - terminate connection (and any multiplexed sessions)
    ~B - send a BREAK to the remote system
    ~C - open a command line
    ~R - request rekey
    ~V/v - decrease/increase verbosity (LogLevel)
    ~^Z - suspend ssh
    ~# - list forwarded connections
    ~& - background ssh (when waiting for connections to terminate)
    ~? - this message
    ~~ - send the escape character by typing it twice
```

{% hint style="info" %}
(Note that escapes are only recognized immediately after newline.
{% endhint %}

## <mark style="color:red;">**Mounting Filesystems**</mark>

NFS, while venerable, is pretty shoddy when it comes to securely sharing directories. You can share to an entire subnet, but anything beyond that gets tricky. Enter sshfs, the ssh _filesystem_.

I’ve got a machine called `wonka` trying to share the directory `/srv/factory` with the host `bucket`. Assuming that the user `charlie` on `bucket` has keys set up on `wonka`, and assuming that I’ve got sshfs available (install it via your package manager of choice if `which sshfs` returns nothing), try this out:

```
sshfs wonka:/srv/factory /mnt -o idmap=user
```

Everything is pretty self-explanatory except for the filesystem mount option `idmap`. That just tries to map the unix numeric IDs from the server to the user you’re mounting the filesystem as (it’s the least troublesome option.)

And presto, you’re sharing a filesystem quickly, easily, and securely. The caveat here is that filesystem changes can take a little longer to propagate than with similar file-sharing schemes like SMB or NFS.

## <mark style="color:red;">**Remote File Editing**</mark>

This trick relies on the netrw capabilities of vim (a directory browsing function) and scp (a sister program of ssh.)

Netrw allows vim to browse the filesystem on the local filesystem easily (try it in a terminal with `$ vim [path]`, such as `$ vim .`. However, we can pass vim a _remote path_ using `scp://` to achieve remote file editing:

```
vim scp://wonka/
```

You’re now browsing your home directory’s contents on `wonka` within vim. Any file edits you make will be synced over on write via scp.

## <mark style="color:red;">**Accessing NAT-Ed Hosts Directly**</mark>

ssh config files follow this syntax:

```
Host foobar
    Option value
```

For example, you can reduce the command\*\*:\*\*

```
ssh -p12345 foo@bar.baz.edu -i ~/.ssh/customkey
```

to

```
 ssh bar
```

by adding the following to `~/.ssh/config`:

```
Host bar
    User foo
    Port 12345
    IdentityFile ~/.ssh/customkey
    HostName bar.baz.edu
```

Now that we’ve dabbled in the config, look at this configuration stanza:

```
Host behind.bar
    # ProxyCommand ssh bar exec nc behind %p
    # I've since been educated that invoking netcat for forwarding
    # is deprecated, use the -W flag instead:
    ProxyCommand ssh -q -W %h:%p bar
```

`ProxyCommand` directs ssh how to connect to `behind.bar`: connect to `bar` (previously defined) and ~~use netcat to~~ connect to the port that ssh would normally connect to. ssh’s `-W` flag intelligently forwards traffic to the interpolated %h (hostname) and %p (port) variable placeholders. The following diagram demonstrates.

![](<../.gitbook/assets/image (291) (1).png>)

you can essentially treat the NAT-ed host as if there were no intermediate hosts between the ssh client and daemon.

## <mark style="color:red;">**Sharing Connections**</mark>

While you _could_ negotiate another asymmetric ssh handshake, why not use your pre-existing connection to make speedier file copies or logins?

Use `Controlpath`:

```
Host busyserver
    Controlmaster auto
    Controlpath ~/.ssh/ssh-%r@%h:%p.sock
```

This means upon first connection to `busyserver`, ssh will create a socket in `~/.ssh/` which can be shared by other commands. If you’re using commands like rsync or scp, this can make repetitive copy tasks much quicker.

## <mark style="color:red;">Sidedoor</mark>

{% embed url="https://github.com/daradib/sidedoor" %}

sidedoor maintains an SSH connection or tunnel with a shell script daemon.

