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

## <mark style="color:red;">Set Security Mount Options on Directories</mark>

## <mark style="color:orange;">**Set nodev Option**</mark>** **&#x20;

The `nodev` mount option specifies that the filesystem cannot contain special devices.&#x20;

### /tmp:

Since the `/tmp` filesystem is not intended to support devices, set this option to ensure that users cannot attempt to create block or character special devices in `/tmp`.

Edit the `/etc/fstab` file and add `nodev` to the fourth field (mounting options) for the /`tmp` partition.

&#x20;See the fstab(5) manual page for more information.&#x20;

Run the following command to remount `/tmp` :

&#x20;`# mount -o remount,nodev /tmp`

### **/var/tmp:**

`mount | grep /var/tmp`&#x20;

Edit the `/etc/fstab` file and add `nodev` to the fourth field&#x20;

`# mount -o remount,nodev /var/tmp`

### **/home:**

`mount | grep /home`&#x20;

Edit the `/etc/fstab` file and add `nodev` to the fourth field:

`# mount -o remount,nodev`&#x20;

### **dev/shm:**

`mount | grep /dev/shm`

&#x20;Edit the `/etc/fstab` file and add `nodev` to the fourth field:

`# mount -o remount,nodev`&#x20;

### **removable media partitions:**

Run the following command and verify that the `nodev` option is set on all removable media partitions &#x20;

`mount`

&#x20;Edit the `/etc/fstab` file and a**dd `nodev` to the fourth field**

## <mark style="color:orange;">**Set nosuid Option**</mark>** **&#x20;

### /tmp:

If a `/tmp` partition exists run the following command and verify that the `nosuid` option is set on `/tmp:`&#x20;

`# mount | grep /tmp`&#x20;

Edit the `/etc/fstab` file and add `nosuid` to the fourth field (mounting options) for the `/tmp` partition Run the following command to remount `/tmp` :&#x20;

\# `mount -o remount,nosuid /tmp`

### **/var/tmp:**

`mount | grep /var/tmp`&#x20;

Edit the `/etc/fstab` file and add `nosuid` to the fourth field:

`# mount -o remount,nosuid`&#x20;

### **/dev/shm:**

`mount | grep /dev/shm`

&#x20;Edit the `/etc/fstab` file and add `nosuid` to the fourth field:

`# mount -o remount,nosuid /dev/shm`

### **removable media partitions:**

Run the following command and verify that the `nosuid` option is set on all removable media partitions&#x20;

`mount`&#x20;

Edit the `/etc/fstab` file and add `nosuid` to the fourth field

## <mark style="color:orange;">**Set noexec Option**</mark>** **&#x20;

### **/var/tmp:**

`mount | grep /var/tmp`&#x20;

Edit the `/etc/fstab` file and add `noexec` to the fourth field:

`# mount -o remount,noexec /var/tmp`

### **/dev/shm:**

`mount | grep /dev/shm`&#x20;

Edit the `/etc/fstab` file and add `noexec` to the fourth field:

`#mount -o remount,noexec /dev/shm`



### **removable media partitions:**

Run the following command and verify that the `noexec` option is set on all removable media partitions&#x20;

`mount`

&#x20;Edit the `/etc/fstab` file and add `noexec` to the fourth field

## <mark style="color:red;">Sticky Bit</mark>

Run the following command to verify no world writable directories exist without the sticky bit set:

`# df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -type d \( -perm -0002 -a ! -perm -1000 \) 2>/dev/null`&#x20;

No output should be returned Run the following command to set the sticky bit on all world writable directories:

&#x20;`df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -type d -perm -0002 2>//dev/null | xargs chmod a+t`

































