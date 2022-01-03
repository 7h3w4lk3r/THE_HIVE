# Users / Groups



![](<../../../.gitbook/assets/image (282) (1).png>)

## <mark style="color:red;">User Management</mark>

### <mark style="color:orange;">Create User</mark>

```
useradd [username]

# options
-g   Main user group (gid)
-d   User Home directory, by default located at /home/<UserName>
-m  Create Home directory in case it doesn’t exist.
-s   Assign a specific shell to the user, by default it is /bin/bash.
-M dont create home directory

# example:
useradd -g sector1 -d /home/nicolas -m -s /bin/bash nicolas
sudo useradd -G wheel -m -d /home/frank -s /bin/bash frank


adduser [username]
```

{% hint style="info" %}
While the command useradd runs a system binary file the command adduser is a perl script to interact with useradd With the adduser command, the advantage is the home is created automatically. If using the command adduser we need to specify the -m option.
{% endhint %}

### <mark style="color:orange;">Add Root User</mark>

```
useradd -ou 0 -g 0 [username]
```

#### add user to sudoers group (user will be created of doesnt exist)

```
//usermod -aG [group] [username]
usermod -aG [group1,group2,group3,...] [username]
```

### <mark style="color:orange;">Change User Home Directory</mark>

```
usermod -d [dir] [username]
```

### <mark style="color:orange;">Lock/Unlock User</mark>

```
usermod -L [username]
usermod -U [username]
```

### <mark style="color:orange;">Set New UID for User</mark>

```
usermod -u [username]
```

### <mark style="color:orange;">Deleting User</mark>

```
userdel [username]
```

### <mark style="color:orange;">Password</mark>

```
set password
passwd [username]
```

### <mark style="color:orange;">List commands you are allowed to run</mark>

```
sudo -l
sudo -ll
```

### <mark style="color:orange;">Change User Shell</mark>

```
chsh -s [shell ] [username]
chsh -s /bin/bash hesher
```

### <mark style="color:orange;">List commands another USER is allowed</mark>

```
sudo -l -U user
```

### <mark style="color:orange;">sudo Commands</mark>

```
Run as root:
sudo COMMAND

 Run as USER:
sudo -u USER COMMAND

Get a shell
sudo -s
sudo -s -u USER
```

### <mark style="color:orange;">Account Aging</mark>

```
sudo useradd -e 2020-12-31 charlie
```

you can set the number of days before an account with an expired password will get locked out:

```
sudo usermod -f 5 charlie
```

### <mark style="color:orange;">Account Expiration Date</mark>

```
  -I, --inactive INACTIVE       set password inactive after expiration
  -m, --mindays MIN_DAYS        set minimum number of days before password
  -M, --maxdays MAX_DAYS        set maximum number of days before password
  -W, --warndays WARN_DAYS      set expiration warning days to WARN_DAYS
  -d, --lastday LAST_DAY        set date of last password change to LAST_DAY

chage -M 20 john

chage -E 2020-12-31 charlie

```

### <mark style="color:orange;">Default Account Aging</mark>

for Red Hat or CentOS only

```
/etc/default/useradd

# useradd defaults file
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
```

Ubuntu also has the useradd configuration file, but it doesn't work. No matter how you configure it, the Ubuntu version of useradd just won't read it. So, the write-up about this file only applies to Red Hat or CentOS.

### <mark style="color:orange;">Login/Logout Monitoring</mark>

#### monitor last logins

```
last
lastb
lastlog → last time a user has logged in
```

### <mark style="color:orange;">Authentication Log Files</mark>

```
/var/log/messages
/var/log/syslog
/var/log/secure
/var/log/auth.log
```

## <mark style="color:red;">Configure Root Access</mark>

you can disable root logins entirely by setting root’s encrypted password to \* or to some other fixed, arbitrary string. On Linux, passwd -l “locks” an account by prepending a ! to the encrypted password, with equivalent results. The \* and the ! are just conventions; no software checks for them explicitly. Their effect derives from their not being valid password hashes. As a result, attempts to verify root’s password simply fail.

### <mark style="color:orange;">make sure only root has UID 0</mark>

```
awk -F: '($3 == "0")'  /etc/passwd
```

### <mark style="color:orange;">Converting Passwords</mark>

```
pwconv - convert to shadow passwords.
pwunconv - convert from shadow passwords.
```

### <mark style="color:orange;">/etc/shadow Format</mark>

```
Username
Hashed password
Days since epoch of last password change
Days until change allowed
Days before change required
Days warning for expiration
Days before account inactive
Days since epoch when account expires
Reserved
```

## <mark style="color:red;">Lock/Unlock the Password</mark>

```
passwd -l account
passwd -u account 
```

### <mark style="color:orange;">set the account shell to no login</mark>

```
/sbin/nologin
```

![](<../../../.gitbook/assets/image (290) (1) (1).png>)

### <mark style="color:orange;">Using chsh Command</mark>

```
chsh -s SHELL ACCOUNT
chsh -s /sbin/nologin jason
```

## <mark style="color:red;">Manually Adding Users</mark>

#### Edit `/etc/passwd` with view and add a new line for the new account. Be careful with the syntax. Do not edit directly with an editor! vipw locks the file, so that other commands won't try to update it at the same time. You should make the password field be \`\*', so that it is impossible to log in.

#### Similarly, edit `/etc/group` if you need to create a new group as well.

#### Create the home directory of the user with mkdir.

#### Copy the files from `/etc/skel` to the new home directory.

Fix ownerships and permissions with chown and chmod. The -R option is most useful. The correct permissions vary a little from one site to another, but usually the following commands do the right thing:

```
cd /home/newusername
chown -R username.group .
chmod -R go=u,go-w .
chmod go= .
```

#### Set the password with passwd.

## <mark style="color:red;">Group Management</mark>

### <mark style="color:orange;">Create a New Group</mark>

```
groupadd [name]
```

### <mark style="color:orange;">Change Group Name</mark>

```
groupmod -n [new name] [old name] 
```

### <mark style="color:orange;">Change the Group ID</mark>

```
groupmod -q 4000 [group]
```

```
chmod g+rwx [group]
chown [user]:[group] [file]
```

### <mark style="color:orange;">set GID</mark>

```
chmod g+s [group]
```

### <mark style="color:orange;">change primary group</mark>

```
usermod -g [group] [usename]
groupmod -g [gid] -n [new name] [oldname]
```

### <mark style="color:orange;">remove a user from a group</mark>

```
gpasswd -d [username] [groupname]
```

### <mark style="color:orange;">remove a group</mark>

```
groupdel [name]
```

### <mark style="color:orange;">list all system groups</mark>

```
cat /etc/groups
```

### <mark style="color:orange;">list current user groups</mark>

```
groups
```

### <mark style="color:orange;">list groups of another account</mark>

```
groups [username]
ip [username]
```
