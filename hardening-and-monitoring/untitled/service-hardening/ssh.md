# SSH

## <mark style="color:red;">Understanding SSH encryption algorithms</mark>

* <mark style="color:orange;">Ciphers :</mark> These are the symmetric algorithms that encrypt the data that the client and server exchange with each other.
* <mark style="color:orange;">HostKeyAlgorithms :</mark> This is the list of host key types that the server can use.
* <mark style="color:orange;">KexAlgorithms :</mark> These are the algorithms that the server can use to perform the symmetric key exchange.
* <mark style="color:orange;">MAC :</mark> Message Authentication Codes are hashing algorithms that cryptographically sign the encrypted data in transit. This ensures data integrity and will let you know if someone has tampered with your data.

## <mark style="color:red;">Scanning for enabled SSH algorithms</mark>

We have two good ways to scan an SSH server. If your server is accessible via the internet, you can go to the SSHCheck site at https:/​ / ​ sshcheck.​ com/

Then, just type in either the IP address or hostname of your server. If you've changed the port from the default port 22 , enter the port number as well. When the scan completes, you'll see the list of enabled algorithms, along with recommendations on which ones to either enable or disable.

If the machine that you want to scan isn't accessible from the internet, or if you would rather use a local scanning tool, you can install ssh\_scan.

```
sudo apt install ruby gem
sudo gem install ssh_scan
```

ssh\_scan executable will be installed in the /usr/local/bin/ directory.

```
sudo ssh_scan -t 192.168.0.7
```

save the output to a .json file

```
sudo ssh_scan -t 178.60.214.30 -p 222 -o ssh_scan-178-60-214-30.json
sudo ssh_scan -t 192.168.0.7 -o ssh_scan-7.json
```

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

## <mark style="color:red;">SSH Config Files</mark>

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

### <mark style="color:orange;">Creating a group and configuring the sshd\_config file</mark>

```
sudo groupadd sftpusers
sudo useradd -G sftpusers max
sudo useradd -m -d /home/max -s /bin/bash -G sftpusers max
```

Open the `/etc/ssh/sshd_config` file in your favorite text editor. Find the line that says the following:

```
Subsystem sftp /usr/lib/openssh/sftp-server
```

Change it to the following:

```
Subsystem sftp internal-sftp
```

This setting allows you to disable normal SSH login for certain users. At the bottom of the sshd\_config file, add a Match Group stanza:

```
Match Group sftpusers
    ChrootDirectory /home
    AllowTCPForwarding no
    AllowAgentForwarding no
    X11Forwarding no
    ForceCommand internal-sftp
```

An important consideration here is that the ChrootDirectory has to be owned by the root user, and it can't be writable by anyone other than the root user. When Max logs in, he'll be in the /home directory, and will then have to cd into his own directory. This also means that you want all your users' home directories to have the restrictive 700 permissions settings, in order to keep everyone out of everyone else's stuff.

Save the file and restart the SSH daemon.

## <mark style="color:red;">Jail SSH user to home directory on Linux</mark>

Jailing an SSH user to their home directory allows you (the administrator) to exercise a lot of control and security over the user accounts on a Linux system.

{% hint style="info" %}
The jailed user still has access to their home directory, but can’t traverse the rest of the system. This keeps everything else on the system private and will prevent anything from being tampered with by an SSH user. It’s an ideal setup for a system that has various users and each user’s files need to stay private and isolated from the others.
{% endhint %}

{% embed url="https://www.tecmint.com/restrict-ssh-user-to-directory-using-chrooted-jail" %}

## <mark style="color:red;">fail2ban</mark>

{% embed url="https://cloudcone.com/docs/article/how-to-install-fail2ban-on-centos-7" %}

#### Protect SSH server from brute force attacks

{% hint style="danger" %}
In case of a failure in the fail2ban service you might get locked out of the system or not be able to login.
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

### <mark style="color:orange;">unban a client</mark>

use the following command with the jail name to view banned IPs:

```
sudo fail2ban client status <jail_name>
sudo fail2ban client status sshd
```

To revoke an IP address in fail2ban and remove it from the jail, use the following syntax:

```
# unban ip from all jails
fail2ban-client unban [ip]

sudo fail2ban-client to adjust jail_name unbanip xxx.xxx.xxx.xxx
sudo fail2ban-client to adjust sshd unbanip 192.168.72.186
```

## <mark style="color:red;">Configuring automatic logout for both local and remote users</mark>

This first method will automatically log out idle users who are logged on either at the local console or remotely via SSH. Go into the /etc/profile.d/ directory and create the autologout.sh file with the following contents:

```
TMOUT=100
readonly TMOUT
export TMOUT
```

This sets a timeout value of 100 seconds. ( TMOUT is a Linux environmental variable that sets timeout values.)

Set the executable permission for everybody:

```
sudo chmod +x autologout.sh
```

Log out and then log back in. Then, let the VM sit idle. After 100 seconds, you should see that the VM is back at the login prompt

though, that if any users are already logged in at the time you create this file, the new configuration won't take effect for them until they log out and then log back in.

### <mark style="color:orange;">Configuring automatic logout in sshd\_config</mark>

The second method only logs out users who are logged in remotely via SSH. Instead of creating the /etc/profile.d/autologout.sh file, look for these two lines in the `/etc/ssh/sshd_config` file:

```
ClientAliveInterval 100
ClientAliveCountMax 0
```

Then, restart the SSH service to make the change take effect.

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

{% hint style="info" %}
for Redhat the log file is in /var/log/secure

for Debian the log file is in /var/log/auth.log
{% endhint %}

### <mark style="color:orange;">Events of ssh down</mark>

```
grep -R "ssh.*Received signal 15" /var/log/auth.log
```

### <mark style="color:orange;">Events of ssh up</mark>

```
grep -R "sshd.*Server listening" /var/log/auth.log
```

### <mark style="color:orange;">Events of ssh failed login</mark>

```
grep -R "sshd.*Failed password for invalid user" /var/log/auth.log
grep -R "sshd.*Failed" /var/log/auth.log
```

### <mark style="color:orange;">Events of ssh break-in attemp</mark>

```
grep -R "sshd.*POSSIBLE BREAK-IN ATTEMPT!" /var/log/auth.log
```

### <mark style="color:orange;">Events of ssh port scap</mark>

```
grep -R "sshd.*Bad protocol version identification" /var/log/auth.log
```

### <mark style="color:orange;">Events of ssh login by public key</mark>

```
grep -R "sshd.*Accepted publickey for" /var/log/auth.log
```

### <mark style="color:orange;">Events of ssh login by password</mark>

```
grep -R "sshd.*Accepted password for" /var/log/auth.log
```

### <mark style="color:orange;">Events of ssh logout event</mark>

```
grep -R "sshd.*pam_unix(sshd:session): session closed for" /var/log/auth.log
```

## <mark style="color:red;">Configuring more detailed logging</mark>

Open the `sshd_config` man page and scroll down to the LogLevel item. There, you'll see the various settings that provide different levels of detail for logging SSH messages

Open the `/etc/ssh/sshd_config` file in your favorite text editor. Find the line that says the following:

```
#LogLevel INFO

Change it to the following:

LogLevel DEBUG3

sudo systemctl restart ssh
```

## <mark style="color:red;">Configuring access control with whitelists and TCP Wrappers</mark>

we can also set up a couple of access control mechanisms that will allow only certain users, groups, or client machines to log in to an SSH server. These two mechanisms are as follows:

#### Whitelists within the sshd\_config file, TCP Wrappers, via the `/etc/hosts.allow` and `/etc/hosts.deny` files

### <mark style="color:orange;">Configuring whitelists within sshd\_config</mark>

The four access control directives that you can set within sshd\_config are as follows:

```
DenyUsers
AllowUsers
DenyGroups
AllowGroups
```

For each directive, you can specify more than one username or group name, separating them with a blank space.

```
AllowUsers donnie
sudo systemctl restart ssh
```

### <mark style="color:orange;">Configuring whitelists with TCP Wrappers</mark>

TCP Wrappers – singular, not plural – listens to incoming network connections, and either allows or denies connection requests. Whitelists and blacklists are configured in the /etc/hosts.allow file and the /etc/hosts.deny file. Both of these files work together. If you create a whitelist in hosts.allow without adding anything to hosts.deny , nothing will be blocked. That's because TCP Wrappers consults hosts.allow first, and if it finds a whitelisted item there, it will just skip over looking in hosts.deny .

If a connection request comes in for something that isn't whitelisted, TCP Wrappers will consult hosts.allow , find that there's nothing there for the source of this connection request, and then will consult hosts.deny . If nothing is in hosts.deny , the connection request will still go through. So, after you configure hosts.allow , you have to also configure hosts.deny in order to block anything.

To whitelist a single IP address, place a line like this into the /etc/hosts.allow file:

```
/SSHD: 192.168.0.225
```

Then, place this line into the /etc/hosts.deny file:

```
SSHD: ALL
```

Now, if you try to log in from anywhere else besides the IP address that's listed in hosts.allow , you will be denied access.

## <mark style="color:red;">SSH Tools</mark>

### <mark style="color:orange;">Export local env to Internet</mark>

{% embed url="https://ngrok.com" %}

### <mark style="color:orange;">SSH by auto input password</mark>

```
apt install sshpass
```

```
sshpass sshpass -p “$PASSWORD” ssh -o StrictHostKeyChecking=no $username@$sship=
```

## <mark style="color:red;">Scripts</mark>

### <mark style="color:orange;">Inject local key to remote ssh server server</mark>

```
// Some cat ~/.ssh/id_rsa.pub | ssh $username@$ssh_hostk "cat - >> ~/.ssh/authorized_keys"

ssh $username@$ssh_hostk "cat ~/.ssh/authorized_keys"
```

### <mark style="color:orange;">Use expect to run ssh command with credential auto input</mark>

```
#!/usr/bin/expect
set timeout 20
set command "cat /etc/hosts"
set user "vagrant"
set password "vagrant"
set ip "192.168.50.10"
spawn ssh -o stricthostkeychecking=no $user@$ip "$command"
expect "*password:*"
send "$password\r"
expect eof;
```

### <mark style="color:orange;">SSH reverse tunnel with autossh</mark>

```
autossh -M 40000 -p 2702 -i /home/denny/al -fN \
    -o "PubkeyAuthentication=yes" \
    -o "StrictHostKeyChecking=false" -o "PasswordAuthentication=no" \
    -o "ServerAliveInterval 60" -o "ServerAliveCountMax 3" \
    -R 123.57.240.189:29995:localhost:22 root@123.57.240.189
```
