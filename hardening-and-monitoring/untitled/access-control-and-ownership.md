# Access Control & Ownership

## <mark style="color:red;">SELinux</mark>

{% embed url="https://linuxhint.com/a-beginners-guide-to-selinux-on-centos" %}

{% embed url="https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/index" %}

{% embed url="https://wiki.gentoo.org/wiki/Category:SELinux" %}

## <mark style="color:red;">AppArmore</mark>

{% embed url="https://kubernetes.io/docs/tutorials/clusters/apparmor" %}

{% embed url="https://wiki.debian.org/AppArmor/HowToUse" %}

## <mark style="color:red;">chmod</mark>

#### <mark style="color:red;">change access modes / permissions</mark>

![](<../../.gitbook/assets/image (298) (1) (1) (1).png>)

![](<../../.gitbook/assets/image (294) (1) (1).png>)

### <mark style="color:orange;">chmod Recursively</mark>

```
chmod -R g+w mydir
```

### <mark style="color:orange;">copy permissions from another file</mark>

```
chmod --reference=filea fileb
```

{% hint style="info" %}
If you want to adjust execute bits, be wary of chmod -R. It’s blind to the fact that the execute bit has a different interpretation on a directory than it does on a flat file. Therefore, chmod -R a-x probably won’t do what you intend. Use find to select only the regular files:

`find mydir -type f -exec chmod a-x {} ';'`
{% endhint %}

## <mark style="color:red;">Sticky Bit</mark>

#### <mark style="color:red;">restrict renaming/deleting files</mark>

A Sticky bit is a permission bit that is set on a file or a directory that lets only the owner of the file/directory or the root user to delete or rename the file. No other user is given privileges to delete the file created by some other user.

Now, turn ON/OFF the sticky bit on the directory by using +t flag of chmod command.

```
# turn on
chmod +t allAccess/

# turn off
chmod -t allAccess/
```

to set sticky bit on a file with numeric permission format, give 1 before you specify other numbered privileges, as shown below. The example below, gives rwx permission to user, group and others (and also adds the sticky bit to the directory).

```
chmod 1777 dir
```

## <mark style="color:red;">chown & chgrp</mark>

#### <mark style="color:red;">change user/group ownership</mark>

The chown command changes a file’s ownership, and the chgrp command changes its group ownership. The syntax of chown and chgrp mirrors that of chmod, except that the first argument is the new owner or group, respectively.

```
sudo chown -R matt ~matt/restore
sudo chgrp -R staff ~matt/restore
```

chown can change both the owner and group of a file at once with the syntax

```
chown user:group file
sudo chown -R matt:staff ~matt/restore
```

### <mark style="color:orange;">Creating a Shared Directory</mark>

```
sudo mkdir marketing
sudo chown nobody:marketing marketing
sudo chmod 770 marketing

# or set SGID
sudo chmod 2770 marketing

# set sticky bit
sudo chmod 3770 marketing

drwxrws--T. 2 nobody marketing 30 Nov 13 16:03 marketing
```

## <mark style="color:red;">UMASK</mark>

#### <mark style="color:red;">assign default permissions</mark>

You can use the built-in shell command umask to influence the default permissions given to the files you create. Every process has its own umask attribute; the shell’s built-in umask command sets the shell’s own umask, which is then inherited by commands that you run.

![](<../../.gitbook/assets/image (303).png>)

For example, umask 027 allows all permissions for the owner but forbids write permission to the group and allows no permissions for anyone else.

The file creation mask can be set using octal or symbolic notation. To make the changes permanent, set the new `umask` value in a global configuration file like `/etc/profile` file which will affect all users or in a user’s shell configuration files such as `~/.profile`, `~/.bashrc` or `~/.zshrc`, which will affect only the user. The user files have precedence over the global files.

Before making changes to the `umask` value, make sure the new value doesn’t pose a potential security risk. Values less restrictive than `022` should be used with great caution. For example, `umask 000` means anyone has read, write, and execute permissions on all newly created files.

Let’s say we want to set more restrictive permissions for the newly created files and directories so others will not be able to `cd` to the directories and read files. The permissions we want are `750` for directories and `640` for files.

To calculate the `umask` value, simply subtract the desired permissions from the default one:

Umask value: `777-750 = 027`

The desired `umask` value represented in numeric notation is `027`.

To permanently set the new value system-wide, open the `/etc/profile` file with your text editor:

```
sudo nano /etc/profileCopy
```

and change or add the following line at the beginning of the file:

`/etc/profile`

```
umask 027
```

For changes to take effect, run the following `source` command or log out and log in:

```
source /etc/profileCopy
```

To verify the new settings, we will create one new file and directory using [`mkdir`](https://linuxize.com/post/how-to-create-directories-in-linux-with-the-mkdir-command/) and [`touch`](https://linuxize.com/post/linux-touch-command/) :

```
mkdir newdirtouch newfileCopyCopy
```

If you check the permissions using the `ls` command, you will notice that the new file has `640` and the new directory `750` permissions, as we wanted:

```
drwxr-x--- 2 linuxize users 4096 Jul  4 18:14  newdir
-rw-r----- 1 linuxize users    0 Jul  4 18:14  newfile
Copy
```

Another way to set the file creation mask is by using symbolic notation. For example `umask u=rwx,g=rx,o=` is same as `umask 027`

## <mark style="color:red;">ACLs</mark>

#### <mark style="color:red;">set access control list</mark>

ACL = Access Control List

Provides additional control Example: Give one user access to a file. Traditional solution is to create another group.

### <mark style="color:orange;">Ensure file system mounted with ACL support</mark>

```
mount -o acl /path/to/dev /path/to/mount
tune2fs -o acl /path/to/dev

# check:
tune2fs -l /path/to/dev | grep options
```

### <mark style="color:orange;">Types of ACLs</mark>

1. **Access** : Control access to a specific file or directory.
2. Default : Used on directories only. Files without access rules use the default ACL rules. Not retroactive. Optional.

### <mark style="color:orange;">Creating ACLs</mark>

`setfacl` and `getfacl` are used for setting up ACL and showing ACL respectively.

```
setfacl -m ACL FILE_OR_DIRECTORY
```

![](<../../.gitbook/assets/image (290) (1).png>)

Users and groups can be identified by name or by UID/GID. The exact number of entries that an ACL can contain varies with the filesystem implementation but is usually at least 32.

### <mark style="color:orange;">User ACLs / Rules</mark>

u:uid:perms Set the access ACL for a user.

```
tfacl -m u:jason:rwx start.sh
setfacl -m u:sam:xr start.sh
```

### <mark style="color:orange;">Group ACLs / Rules</mark>

```
g:gid:perms Sets the access ACL for a group.
setfacl -m g:sales:rw sales.txt
```

### <mark style="color:orange;">Mask ACLs / Rules</mark>

```
m:perms >>> Sets the effective rights mask.
setfacl -m m:rx sales.txt
```

### <mark style="color:orange;">Other ACLs / Rules</mark>

```
o:perms >>> Sets the access ACL for others.
setfacl -m o:r sales.txt
```

### <mark style="color:orange;">Creating Multiple ACLs at Once</mark>

```
setfacl -m u:bob:r,g:sales:rw sales.txt
```

### <mark style="color:orange;">Default ACLs</mark>

```
d:[ugo]:perms  >>> Sets the default ACL.
setfacl -m d:g:sales:rw sales
```

### <mark style="color:orange;">Setting ACLs Recursively (-R)</mark>

```
setfacl -R -m g:sales:rw sales
```

### <mark style="color:orange;">Removing ACLs</mark>

```
setfacl -x ACL FILE_OR_DIRECTORY
setfacl -x u:jason sales.txt
setfacl -x g:sales sales.txt
setfacl -b sales.txt
```

### <mark style="color:orange;">Viewing ACLs</mark>

```
getfacl sales.txt
```

### <mark style="color:orange;">Backup ACL-enabled Files</mark>

if you're using tar for backup, use `--acls` to keep the ACLs when creating an archive.

```
tar cJvf new_perm_dir_backup.tar.xz new_perm_dir/ --
acls

tar xJvf new_perm_dir_backup.tar.xz --acls
new_perm_dir/
```

### <mark style="color:orange;">Detecting Files with ACLs</mark>

![](<../../.gitbook/assets/image (275).png>)

example commands:

To add permission for user

```
setfacl -m "u:user:permissions" /path/to/file
```

To add permissions for a group

```
setfacl -m "g:group:permissions" /path/to/file
```

To allow all files or directories to inherit ACL entries from the directory it is within.

```
setfacl -dm "entry" /path/to/dir
```

To remove a specific entry

```
setfacl -x "entry" /path/to/file
```

To remove all entries

```
setfacl -b path/to/file
```

example:

```
setfacl -m u:mandeep:r-x test/declarations.h
```

Using the tar `--acls` option to prevent the loss of ACLs during a backup

```
cd perm_demo_dir
tar cJvf new_perm_dir_backup.tar.xz new_perm_dir/ --acls
```

### <mark style="color:orange;">ACLs in Shared Directories</mark>

First, sets the normal permissions:

```
chmod 600 file1.txt
```

then set the ACL for user2:

```
setfacl -m u:user2:r file1.txt
```

## <mark style="color:red;">File Attribute</mark>

Linux defines a set of supplemental flags that can be set on files to request special handling. For example, the a flag makes a file append-only, and the i flag makes it immutable and undeletable

Linux uses the commands lsattr and chattr to view and change file attributes

![](<../../.gitbook/assets/image (297) (1) (1).png>)

### <mark style="color:orange;">Viewing Attributes</mark>

```
lsattr /etc/motd
```

### <mark style="color:orange;">Modifying Attributes</mark>

Use the chattr command.

```
+ adds attributes.
- removes attributes.
= sets the exact attributes.

chattr +a /var/log/messages
chattr -a /var/log/messages
```
