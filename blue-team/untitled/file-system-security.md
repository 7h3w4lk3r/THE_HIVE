# File System Security

## <mark style="color:red;">Pre-installation Percussions</mark>

When installing a fresh OS, its better to keep these directories in separate partitions to prevent **local attacks, starvation and lower the privilege escalation vector:**

```
/boot
/home
/usr
/var 
/tmp 
/var/tmp
/var/log 
/var/log/audit
```

## <mark style="color:red;">Directory Security Mount Options</mark>&#x20;

### <mark style="color:orange;">**nodev Option**</mark>** **&#x20;

The `nodev` mount option specifies that the filesystem cannot contain special devices.&#x20;

#### <mark style="color:green;">/tmp</mark>

Since the `/tmp` filesystem is not intended to support devices, set this option to ensure that users cannot attempt to create block or character special devices in `/tmp`.

Edit the `/etc/fstab` file and add `nodev` to the fourth field (mounting options) for the /`tmp` partition.

&#x20;See the fstab(5) manual page for more information.&#x20;

Run the following command to remount `/tmp` :

&#x20;`# mount -o remount,nodev /tmp`

#### <mark style="color:green;">**/var/tmp**</mark>

`mount | grep /var/tmp`&#x20;

Edit the `/etc/fstab` file and add `nodev` to the fourth field&#x20;

`# mount -o remount,nodev /var/tmp`

#### <mark style="color:green;">**/hom**</mark>

`mount | grep /home`&#x20;

Edit the `/etc/fstab` file and add `nodev` to the fourth field:

`# mount -o remount,nodev`&#x20;

#### <mark style="color:green;">**dev/shm:**</mark>

`mount | grep /dev/shm`

&#x20;Edit the `/etc/fstab` file and add `nodev` to the fourth field:

`# mount -o remount,nodev`&#x20;

#### <mark style="color:green;">**removable media partitions**</mark>

Run the following command and verify that the `nodev` option is set on all removable media partitions &#x20;

`mount`

&#x20;Edit the `/etc/fstab` file and a**dd `nodev` to the fourth field**

### <mark style="color:orange;">**nosuid Option**</mark>** **&#x20;

#### <mark style="color:green;">/tmp</mark>

If a `/tmp` partition exists run the following command and verify that the `nosuid` option is set on `/tmp:`&#x20;

`# mount | grep /tmp`&#x20;

Edit the `/etc/fstab` file and add `nosuid` to the fourth field (mounting options) for the `/tmp` partition Run the following command to remount `/tmp` :&#x20;

\# `mount -o remount,nosuid /tmp`

#### <mark style="color:green;">**/var/tmp**</mark>

`mount | grep /var/tmp`&#x20;

Edit the `/etc/fstab` file and add `nosuid` to the fourth field:

`# mount -o remount,nosuid`&#x20;

#### <mark style="color:green;">**/dev/shm**</mark>

`mount | grep /dev/shm`

&#x20;Edit the `/etc/fstab` file and add `nosuid` to the fourth field:

`# mount -o remount,nosuid /dev/shm`

#### <mark style="color:green;">**removable media partitions**</mark>

Run the following command and verify that the `nosuid` option is set on all removable media partitions&#x20;

`mount`&#x20;

Edit the `/etc/fstab` file and add `nosuid` to the fourth field

### <mark style="color:orange;">**noexec Option**</mark>** **&#x20;

#### <mark style="color:green;">**/var/tmp**</mark>

`mount | grep /var/tmp`&#x20;

Edit the `/etc/fstab` file and add `noexec` to the fourth field:

`# mount -o remount,noexec /var/tmp`

#### <mark style="color:green;">**/dev/shm**</mark>

`mount | grep /dev/shm`&#x20;

Edit the `/etc/fstab` file and add `noexec` to the fourth field:

`#mount -o remount,noexec /dev/shm`

#### <mark style="color:green;">**removable media partitions**</mark>

Run the following command and verify that the `noexec` option is set on all removable media partitions&#x20;

`mount`

&#x20;Edit the `/etc/fstab` file and add `noexec` to the fourth field

## <mark style="color:red;">Sticky Bit on World-writable Directories</mark>

Run the following command to verify no world writable directories exist without the sticky bit set:

`# df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -type d \( -perm -0002 -a ! -perm -1000 \) 2>/dev/null`&#x20;

No output should be returned Run the following command to set the sticky bit on all world writable directories:

&#x20;`df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -type d -perm -0002 2>//dev/null | xargs chmod a+t`

## <mark style="color:red;">Disable Auto Mounting</mark>

Run the following command to verify autofs is not enabled:

`# systemctl is-enabled autof`

Verify result is not "enabled". to disable autof:

`systemctl disable autofs`

## <mark style="color:red;">Security Related Mount Options</mark>

**nodev :** no device access&#x20;

**nosuid :** no setuid&#x20;

**programs noexec** :  prevent running exec&#x20;

**--remount, noexec** : mount again with no exe

## <mark style="color:red;">File System Integrity</mark>

### <mark style="color:orange;">Creating an Audit Database</mark>

Install AIDE:

`apt install aide aide-common`

Initiate the Database

&#x20;`# aideinit`

**W**hile initializing the database aide will take an snapshot of all system libraries and binaries for future compare to see if anything changed or not copy the new created db in `/var/lib/aide aide.db.new.gz` to `aide.db.gz` do a check for all files for mismatches: ****&#x20;

### <mark style="color:orange;">Check Integrity</mark>

**`# aide --check`**

&#x20;**if there was a change we will see a warning**&#x20;

**Updating the aide DB after checking the aide db:**&#x20;

```
/aide -c aide.conf --update
```

**This will reset the database to the current status of the `/etc/sub-director`**

### <mark style="color:orange;">**Config Files**</mark>

conf file: /etc/aide.conf

&#x20;log file: /etc/log/aide&#x20;

db: /etc/lib/aid

### scheduling

determine if there is a cron job scheduled to run the aide check:

Run the following command:

&#x20;`crontab -u root -e`

Add the following line to the crontab:

```
 0 5 * * * /usr/bin/aide.wrapper --config /etc/aide/aide.conf --check
```

## <mark style="color:red;">File System Permissions</mark>

### <mark style="color:orange;">Permissions on /etc/passwd</mark>

```
 chown root:root /etc/passwd
 chmod 644 /etc/passwd
```

### <mark style="color:orange;">Permissions on /etc/shadow</mark>

```
 chown root:root /etc/shadow
 chown root:shadow /etc/shadow
 chmod o-rwx,g-wx /etc/shadow
```

### <mark style="color:orange;">Permissions on /etc/group</mark>

```
 chown root:root /etc/group
  chmod 644 /etc/group
```

### <mark style="color:orange;">Permissions on /etc/gshadow</mark>

```
chown root:root /etc/gshadow
chown root:shadow /etc/gshadow
chmod o-rwx,g-rw /etc/gshadow
```

### <mark style="color:orange;">Permissions on /etc/passwd Backup Files</mark>

```
chown root:root /etc/passwd-
chmod u-x,go-rwx /etc/passwd
```

## <mark style="color:red;">World-writeable Files</mark>&#x20;

Run the following command and verify no files are returned:

`df --local -P | awk '{if (NR!=1) print $6}' | xargs -I '{}' find '{}' -xdev -type f -perm -0002`

r the following command can be run manually for each partition:

`find [partition] -xdev -type f -perm -0002`

## <mark style="color:red;">Check for Unowned Files or Directories</mark>

Run the following command and verify no files are returned:

`df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -nouser`

&#x20;the following command can be run manually for each partition:

`find [partition]  -xdev -nouser`

## <mark style="color:red;">Check for  Ungrouped Files or Directories</mark>

Run the following command and verify no files are returned:

`df --local -P | awk '{if (NR!=1) print $6}' | xargs -I '{}' find '{}' -xdev -nogroup`

&#x20;the following command can be run manually for each partition:

`find [partition]  -xdev -nogroup`

## <mark style="color:red;">Audit SUID/SGID Executables</mark>

Run the following command to list SUID files:

df --local -P | awk '{if (NR!=1) print $6}' | xargs -I '{}' find '{}' -xdev -type f -perm -4000

df --local -P | awk '{if (NR!=1) print $6}' | xargs -I '{}' find '{}' -xdev -type f -perm -2000

r the following command can be run manually for each partition:

`find [partition] -xdev -type f -perm -4000`

find `[partition]` -xdev -type f -perm -2000

## <mark style="color:red;">Add/remove SUID/SGID Attribute</mark>

Adding the Setuid & Setgid Attributes:

`chmod ug+s /path/to/file`&#x20;

`chmod 6755 /path/to/file`

Removing the Setgid Attribute:

`chmod g-s /path/to/file`

`chmod 0755 /path/to/file`
