# 🔹 Integrity Check

{% embed url="https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/security_hardening/checking-integrity-with-aide_security-hardening" %}

## <mark style="color:red;">**How**</mark> <mark style="color:red;">**File Integrity Checkers work**</mark>

Generally, file integrity checkers work in two phases. The first phase consists of generating a database made up of cryptographically strong hashes for all the critical files on a host. You can think of these hashes as being a unique fingerprint for each file; it’s impossible to change the file without changing its fingerprint. The database usually also contains additional file information such as size, ownership, permissions and modification dates for each file.

In the second phase, once an initial copy of this database is built (the Initial Baseline) the same calculation is periodically recalculated for the same critical files on the host. If a file has been tampered with (let’s say by an attacker who has compromised the host), one of these characteristics will have changed. Detecting these changes will allow the tampered file to be identified, providing a warning of the system compromise.

### <mark style="color:orange;">**File Attributes**</mark>

<mark style="color:green;">**Inode Number:**</mark> This is a unique integer that can be used to index into a data structure (known as an “inode”) that specifies many of the attributes of an active file on a file system. If a file’s inode number changes, that means the file has been deleted and a new file created using the same name as the old one.

<mark style="color:green;">**Type:**</mark> Indicates whether the file is a data file, or a special file such as a named pipe or an interface to a device.

<mark style="color:green;">**Owner:**</mark> Unix associates a unique owner (a Unix account) to each file.

<mark style="color:green;">**Group:**</mark> Unix supports an object that collects multiple Unix accounts into a single entity, known as a “group”. Unix associates a single group with every file.

<mark style="color:green;">**Permission:**</mark> Every file in unix has permission (Read, Write, Execute)

<mark style="color:green;">**Timestamps:**</mark> Three dates are associated with each file. They are the date and time the file’s data was last modified, when the file’s attributes (including its data) was last modified and the time it was last read.

<mark style="color:green;">**Size:**</mark> How large the file is, in bytes. Special files will be zero bytes in size.

<mark style="color:orange;">**Some Commonly Used Integrity Checkers**</mark><mark style="color:orange;">:</mark>

* TripWire
* Advanced Intrusion Detection Environment (AIDE)
* OSSEC

## <mark style="color:red;">AIDE</mark>

### <mark style="color:orange;">Creating an Audit Database</mark>

Install AIDE:

`apt install aide aide-common`

### <mark style="color:orange;">Initiate the Database</mark>

```
# with default config file in /etc/aide.conf
aide -i

# with custom config file
aide --config=/var/aide/aide.conf --init
```

**W**hile initializing the database aide will take an snapshot of all system libraries and binaries for future compare to see if anything changed or not copy the new created db in `/var/lib/aide aide.db.new.gz` to `aide.db.gz` do a check for all files for mismatches:&#x20;

#### replace the old database with the new one:

```
mv /var/aide/aide.db.new.gz /var/aide/aide.db.gz
```

### <mark style="color:orange;">Check Integrity</mark>

```
# with default config file
aide --check

# with custom config file
aide --config=/var/aide/aide.conf --check
```



&#x20;**if there was a change we will see a warning**&#x20;

### <mark style="color:orange;">Update the DB</mark>

**Updating the aide DB after checking the aide db:**&#x20;

```
/aide -c aide.conf --update
```

**This will reset the database to the current status of the `/etc/sub-director`**

### <mark style="color:orange;">**Config Files**</mark>

conf file: /etc/aide.conf

&#x20;log file: /etc/log/aide&#x20;

db: /etc/lib/aid

### <mark style="color:orange;">Scheduling</mark>

determine if there is a cron job scheduled to run the aide check:

Run the following command:

&#x20;`crontab -u root -e`

Add the following line to the crontab:

```
 0 5 * * * /usr/bin/aide.wrapper --config /etc/aide/aide.conf --check
```
