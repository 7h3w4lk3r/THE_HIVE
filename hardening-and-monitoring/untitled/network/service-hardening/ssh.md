# SSH

## <mark style="color:red;">Using a Key Pair</mark>

### <mark style="color:orange;">generate and use SSH key pair</mark>

```
mkdir -p ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t rsa

# copy the pub key to the server via ssh
ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.1.5  
```

(Optional) Set up SSH Agent to store the keys to avoid having to re-enter passphrase at every login:

```
ssh-agent $BASH
ssh-add ~/.ssh/id_rsa
```

now we can use ssh with priv key authentication

```
ssh -i [priv key] user@server
# or just
ssh user@server
```

{% hint style="danger" %}
SSH wont allow SSH key with open permissions, use chmod 600
{% endhint %}

`-T` will disable the pseudo -tty allocation and ignore the .bashrc config file in the current session As this is making our session vulnerable to get closed.

### <mark style="color:orange;">Generate key pair without interaction</mark>

```
ssh-keygen -t rsa -f /tmp/sshkey -N "" -q
```

### <mark style="color:orange;">Remove an entry from known\_hosts file</mark>

```
ssh-keygen -f ~/.ssh/known_hosts -R github.com
```

### <mark style="color:orange;">SSH agent load key</mark>

```
exec ssh-agent bash && ssh-add /tmp/id_rsa, ssh-add
```

### <mark style="color:orange;">SSH list all loaded key</mark>

```
ssh-add -l
```

### <mark style="color:orange;">Add passphrase protection to ssh keyfile</mark>

```
ssh-keygen -p -f id_rsa
```

### <mark style="color:orange;">avoid trying all identity files</mark>

```
ssh -o IdentitiesOnly=yes -i id1.key myuser@myserver.com
```

### <mark style="color:orange;">Convert OpenSSL format to SSH-RSA format</mark>

```
ssh-keygen -f my_ssh.pub -i
```



### <mark style="color:orange;">Using OpenSSL</mark>

```
openssl s_client -connect localhost:30001 -ign_eof
```

to elevate to ssh shell create a key pair and copy the public key to the victim authorized keys file and connect to it using the private key:

```
ssh-keygen -b 2048 -t rsa
echo "KEY" > .ssh/authorized_keys
ssh noob@192.168.56.120 -I .ssh/id_rsa

openssl s_client -connect localhost:30001
```

## &#x20;<mark style="color:red;">SSH Config Files</mark>

```
/etc/ssh/ssh_config
/etc/ssh/sshd_config
```

## <mark style="color:red;">SSH Key Files Permissions</mark>

```
# SSH key file permission 	
chmod 600 ~/.ssh/id_rsa


# SSH folder permission 	
chmod 700 ~/.ssh, chown -R $USER:$USER ~/.ssh


# Authorizedkeys file permission 	
chmod 644 ~/.ssh/authorized_keys
```

## <mark style="color:red;">Mute Warning: Permanently added</mark>

```
ssh -o LogLevel=error
```

## <mark style="color:red;">SSH security</mark>

### <mark style="color:orange;">Disable ssh by password</mark>

```
sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config
```

### <mark style="color:orange;">Disable root login</mark>

```
sed -i 's/^PermitRootLogin yes/#PermitRootLogin yes/'  /etc/ssh/sshd_config
```

### <mark style="color:orange;">Enable/Disable SSH Host Key Checking</mark>

```
#  change ~/.ssh/config
# set :
StrictHostKeyChecking yes
```

## <mark style="color:red;">fail2ban</mark>

#### Protect SSH server from brute force attacks

{% hint style="danger" %}
In case of a failure in the fail2ban service you might get locked out of the system or not be able to login.&#x20;
{% endhint %}

The fail2ban service keeps its configuration files in the /etc/fail2ban directory. There is a file with defaults called jail.conf Since this file can be modified by package upgrades, we should not edit this file in-place, but rather copy it so that we can make our changes safely.

```
awk '{ printf "# "; print; }' /etc/fail2ban/jail.conf | sudo tee /etc/fail2ban/jail.local
```

Once the file is copied, we can open the original jail.conf file to see how things are set up by default

```
sudo nano /etc/fail2ban/jail.conf
```

In this file, there are a few settings you may wish to adjust. The settings located under the \[DEFAULT] section will be applied to all services enabled for fail2ban that are not overridden in the service’s own section.

The <mark style="color:orange;">ignoreip setting</mark> configures the source addresses that fail2ban ignores. By default, it is configured to not ban any traffic coming from the local machine. You could add additional addresses to ignore by adding a \[DEFAULT] section with an ignoreip setting under it to the jail.local file. You can add additional addresses by appending them to the end of the directive, separated by a space

The <mark style="color:orange;">bantime</mark> parameter sets length of time that a client will be banned when they have failed to authenticate correctly. This is measured in seconds. By default, this is set to 600 seconds, or 10 minutes.

The next two parameters that you want to pay attention to are <mark style="color:orange;">findtime and maxretry</mark>. These work together to establish the conditions under which a client is found to be an illegitimate user that should be banned.

The <mark style="color:orange;">maxretry</mark> variable sets the number of tries a client has to authenticate within a window of time defined by findtime, before being banned. With the default settings, the fail2ban service will ban a client that unsuccessfully attempts to log in 3 times within a 10 minute window.

You will want to evaluate the <mark style="color:orange;">destemail, sendername, and mta</mark> settings if you wish to configure email alerts. The destemail parameter sets the email address that should receive ban messages. The sendername sets the value of the “From” field in the email. The mta parameter configures what mail service will be used to send mail. Again, add these to the jail.local file, under the \[DEFAULT] header and set to the proper values if you wish to adjust them.

This parameter configures the action that fail2ban takes when it wants to institute a ban. The value action\_ is defined in the file shortly before this parameter. The default action is to simply configure the firewall to reject traffic from the offending host until the ban time elapses.

If you would like to <mark style="color:orange;">configure email alerts</mark>, add or uncomment the action item to the jail.local file and change its value from action\_ to action\_mw. If you want the email to include the relevant log lines, you can change it to action\_mwl. Make sure you have the appropriate mail settings configured if you choose to use mail alerts.

## <mark style="color:red;">SCP ( secure file transfer )</mark>

### <mark style="color:orange;">Download a remote folder</mark>

```
scp -r ec2-user@<ssh-host>:/home/letsencrypt-20180825 ./
```

### <mark style="color:orange;">Upload a file</mark>

```
scp -i <ssh-keyfile> /tmp/hosts ec2-user@<ssh-host>:/root/
```

### <mark style="color:orange;">Upload a folder</mark>

```
// Soscp -r /tmp/abc/ ec2-user@<ssh-host>:/root/
```

### <mark style="color:orange;">Upload with timestamps/permissions kept</mark>

```
scp -rp /tmp/abc/ ec2-user@<ssh-host>:/root/
```

### <mark style="color:orange;">Mount remote directory as local folder</mark>

```
sshfs name@server:/path/remote_folder /path/local_folder
```

## <mark style="color:red;">SSH Logs</mark>













