# Permissions & Ownership

## <mark style="color:red;">chmod</mark>

![](<../../../.gitbook/assets/image (298).png>)

![](<../../../.gitbook/assets/image (294).png>)

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

## <mark style="color:red;">chown & chgrp</mark>

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

## <mark style="color:red;">UMASK (assign default permissions)</mark>

You can use the built-in shell command umask to influence the default permissions given to the files you create. Every process has its own umask attribute; the shell’s built-in umask command sets the shell’s own umask, which is then inherited by commands that you run.

![](<../../../.gitbook/assets/image (303).png>)

For example, umask 027 allows all permissions for the owner but forbids write permission to the group and allows no permissions for anyone else.

## <mark style="color:red;">ACLs</mark>

ACL = Access Control List

&#x20;Provides additional control Example: Give one user access to a file. Traditional solution is to create another group.

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

![](<../../../.gitbook/assets/image (290).png>)

Users and groups can be identified by name or by UID/GID. The exact number of entries that an ACL can contain varies with the filesystem implementation but is usually at least 32.

### <mark style="color:orange;">User ACLs / Rules</mark>













