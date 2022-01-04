# Update Strategy

{% hint style="danger" %}
**Before updating he system kernel, services or programs, remember to check for any software that might break as a result of a system update/upgrade.**&#x20;

**the machine might be using an old unpatched software that is not compatible with newer versions of your distribution**
{% endhint %}

## <mark style="color:red;">Check Package Manager</mark>

Verify package repositories are configured correctly. Depending on the package management in use one of the following command groups may provide the needed information:

```
# yum repo-list
# apt-cache policy
# zypper repos
```

### <mark style="color:orange;">Ensure GPG keys are configured</mark>

```
# rpm -q gpg-pubkey --qf '%{name}-%{version}-%{release} --> %{summary}\n'
# apt-key list
# zypper repos
```

### <mark style="color:orange;">Validate Packages before Installation</mark>

```
on redhat:
rpm --verify
rpm -Va >>> verify all

on ubuntu:
apt install debsums
debsums -l >>> generate a list 
debsums -c  >>> check all

```

### <mark style="color:orange;">Check Package Integrity</mark>

#### <mark style="color:green;">Debian-based</mark>

```
# single package
debsums [package]

# all installed packages
dpkg -l | egrep '^ii' | awk '{print $2}' | xargs sudo debsums | egrep -v 'OK'

# If you suspect that a file changed and don't know from which package it comes from, try:
dpkg -S /full/path/to/file
```

<mark style="color:green;">Redhat-based</mark>

```
# single package
rpm --checksig [package]
```

## <mark style="color:red;">Updating Debian-based systems</mark>

```
# update the repository
sudo apt update

# upgrade all packages
sudo apt upgrade

# complete distro upgrade (including the kernel)
sudo apt dist-upgrade

# remove old unused package to free up space
apt auto-remove
```

Manual Security Update

To manually install the security-related updates without installing non-security updates, just run the following line of code:

If you want to see if there are any security-related updates available, but don't want to see any non-security updates, use the unattended-upgrade command, like so:

```
sudo unattended-upgrade --dry-run -d
```

To manually install the security-related updates without installing non-security updates, just run the following line of code:

```
sudo unattended-upgrade -d
```

## &#x20;<mark style="color:red;">Debian Auto Update</mark>

You can set up automated security updates on Debian by installing a helpful utility called `unattended-upgrades`

1. update your system with `apt update && apt upgrade` .
2.  Install it running the following command:

    ```
    sudo apt install unattended-upgrades
    ```
3.  After the installation completes, you can enable and start the `unattended-upgrades` service by running the following commands:

    ```
    sudo systemctl enable unattended-upgrades
    sudo systemctl start unattended-upgrades
    ```

    This ensures that the service runs on system startup and is persistent throughout.
4. **You now need to make changes to the configuration file. The default configuration file can be found here at `/etc/apt/apt.conf.d/50unattended-upgrades`. Open it with the text editor of your choice.**

{% hint style="info" %}
The unattended-upgrades package ignores lines that start with `//`, as that line is considered to be a comment. Therefore, if you want a repository to update automatically, you need to remove `//` from that line.
{% endhint %}

In our example, remove `//` from the “security” line if it’s there, `"origin=Debian,codename=${distro_codename},label=Debian-Security";`. This section should look like the following:

```
nano /etc/apt/apt.conf.d/50unattended-upgrades
```

```c
...

Unattended-Upgrade::Origins-Pattern {
        // Codename based matching:
        // This will follow the migration of a release through different
        // archives (e.g. from testing to stable and later oldstable).
        // Software will be the latest available for the named release,
        // but the Debian release itself will not be automatically upgraded.
//      "origin=Debian,codename=${distro_codename}-updates";
//      "origin=Debian,codename=${distro_codename}-proposed-updates";
        "origin=Debian,codename=${distro_codename},label=Debian";
        "origin=Debian,codename=${distro_codename},label=Debian-Security";

        // Archive or Suite based matching:
        // Note that this will silently match a different release after
        // migration to the specified archive (e.g. testing becomes the
        // new stable).
//      "o=Debian,a=stable";
//      "o=Debian,a=stable-updates";
//      "o=Debian,a=proposed-updates";
//      "o=Debian Backports,a=${distro_codename}-backports,l=Debian Backports";
};

```

### <mark style="color:orange;">Blacklisting Packages</mark> <a href="#blacklisting-packages" id="blacklisting-packages"></a>

The `Unattended-Upgrade::Package-Blacklist` section of the configuration file allows you to block upgrades for specific packages.

To block upgrades for specific packages, add the desired package name to the list. In this example, add “apache2” and “vim”:

```
nano /etc/apt/apt.conf.d/50unattended-upgrades
```

```c
...

Unattended-Upgrade::Package-Blacklist {
    // The following matches all packages starting with linux-
//  "linux-";
    "apache2";
    "vim";
    // Use $ to explicitely define the end of a package name. Without
    // the $, "libc6" would match all of them.
//  "libc6$";
//  "libc6-dev$";
//  "libc6-i686$";

    // Special characters need escaping
//  "libstdc\+\+6$";

    // The following matches packages like xen-system-amd64, xen-utils-4.1,
    // xenstore-utils and libxenstore3.0
//  "(lib)?xen(store)?";

    // For more information about Python regular expressions, see
    // https://docs.python.org/3/howto/regex.html
};

...
```

### <mark style="color:orange;">Deleting Dependencies</mark> <a href="#deleting-dependencies" id="deleting-dependencies"></a>

You can explicitly set up the unattended-upgrades service to remove unused dependencies by changing the `Remove-Unused-Kernel-Packages`, `Remove-New-Unused-Dependencies`, and `Remove-Unused-Dependencies` options to true. Remember to remove `//` to uncomment these lines.

```
nano /etc/apt/apt.conf.d/50unattended-upgrades
```

```c
...

// Remove unused automatically installed kernel-related packages
// (kernel images, kernel headers and kernel version locked tools).
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";

// Do automatic removal of newly unused dependencies after the upgrade
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";

// Do automatic removal of unused packages after the upgrade
// (equivalent to apt-get autoremove)
Unattended-Upgrade::Remove-Unused-Dependencies "true";

..
```

### <mark style="color:orange;">Enabling Automatic Upgrades</mark> <a href="#enabling-automatic-upgrades" id="enabling-automatic-upgrades"></a>

To enable automatic updates create a new auto-upgrades file: `/etc/apt/apt.conf.d/20auto-upgrades` using text editor of your choice.

This file allows you to define how often the auto updates take place.

```
nano /etc/apt/apt.conf.d/20auto-upgrades
```

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::AutocleanInterval "7";
```

* **Update-Package-Lists**: `1` enables auto-update, `0` disables.
* **Unattended-Upgrade**: `1` enables auto-upgrade, `0` disables.
* **AutocleanInterval**: Enables auto clean packages for `X` days. The above configuration displays 7 days
  * For example, `APT::Periodic::AutocleanInterval “7”;` means that the system clears the download archive every seven days.

### <mark style="color:orange;">Enable Auto Reboot</mark>

Uncomment the line by removing the leading slashes, and change false to true, like so:

```
Unattended-Upgrade::Automatic-Reboot "true";
```

### <mark style="color:orange;">Testing The Configuration</mark> <a href="#testing-the-configuration" id="testing-the-configuration"></a>

You can perform a dry run to test the configuration. The dry run command runs a test update but no actual changes take place.

You can run the dry run test by using the command:

```
sudo unattended-upgrades --dry-run --debug
```

## <mark style="color:red;">Ubuntu Auto Update</mark>

1. update your system with `apt update && apt upgrade .`
2.  Install it running the following command:

    ```
    sudo apt install unattended-upgrades
    ```
3.  After the installation completes, you can enable and start the `unattended-upgrades` service by running the following commands:

    ```
    sudo systemctl enable unattended-upgrades
    sudo systemctl start unattended-upgrades
    ```

    This ensures that the service runs on system startup and is persistent throughout.
4. You now need to make changes to the configuration file. The default configuration file can be found here at `/etc/apt/apt.conf.d/50unattended-upgrades`. Open it with the text editor of your choice.

In our example, remove `//` from the “security” line if it’s there, `"${distro_id}:${distro_codename}-security";`. This section should look like the following:

```
nano /etc/apt/apt.conf.d/50unattended-upgrades
```

```c
...

Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}";
        "${distro_id}:${distro_codename}-security";
        // Extended Security Maintenance; doesn't necessarily exist for
        // every release and this system may not have it installed, but if
        // available, the policy for updates is such that unattended-upgrades
        // should also install from here by default.
        "${distro_id}ESMApps:${distro_codename}-apps-security";
        "${distro_id}ESM:${distro_codename}-infra-security";
//      "${distro_id}:${distro_codename}-updates";
//      "${distro_id}:${distro_codename}-proposed";
//      "${distro_id}:${distro_codename}-backports";
};

...
```

### <mark style="color:orange;">Blacklisting Packages</mark> <a href="#blacklisting-packages" id="blacklisting-packages"></a>

The `Unattended-Upgrade::Package-Blacklist` section of the configuration file allows you to block upgrades for specific packages.

To block upgrades for specific packages, add the desired package name to the list. In this example, add “apache2” and “vim”:

```c
...

Unattended-Upgrade::Package-Blacklist {
    // The following matches all packages starting with linux-
//  "linux-";
    "apache2";
    "vim";
    // Use $ to explicitely define the end of a package name. Without
    // the $, "libc6" would match all of them.
//  "libc6$";
//  "libc6-dev$";
//  "libc6-i686$";

    // Special characters need escaping
//  "libstdc\+\+6$";

    // The following matches packages like xen-system-amd64, xen-utils-4.1,
    // xenstore-utils and libxenstore3.0
//  "(lib)?xen(store)?";

    // For more information about Python regular expressions, see
    // https://docs.python.org/3/howto/regex.html
};

...
```

### <mark style="color:orange;">Deleting Dependencies</mark> <a href="#deleting-dependencies" id="deleting-dependencies"></a>

You can explicitly set up the unattended-upgrades service to remove unused dependencies by changing the `Remove-Unused-Kernel-Packages`, `Remove-New-Unused-Dependencies`, and `Remove-Unused-Dependencies` options to true. Remember to remove `//` to uncomment these lines.

```c
...

// Remove unused automatically installed kernel-related packages
// (kernel images, kernel headers and kernel version locked tools).
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";

// Do automatic removal of newly unused dependencies after the upgrade
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";

// Do automatic removal of unused packages after the upgrade
// (equivalent to apt-get autoremove)
Unattended-Upgrade::Remove-Unused-Dependencies "true";

...
```

### <mark style="color:orange;">Enabling Automatic Upgrades</mark> <a href="#enabling-automatic-upgrades" id="enabling-automatic-upgrades"></a>

To enable automatic updates create a new auto-upgrades file: `/etc/apt/apt.conf.d/20auto-upgrades` using text editor of your choice.

This file allows you to define how often the auto updates take place.

```
/etc/apt/apt.conf.d/20auto-upgrades
```

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::AutocleanInterval "7";
```

* **Update-Package-Lists**: `1` enables auto-update, `0` disables.
* **Unattended-Upgrade**: `1` enables auto-upgrade, `0` disables.
* **AutocleanInterval**: Enables auto clean packages for `X` days. The above configuration displays 7 days
  * For example, APT::Periodic::AutocleanInterval “7”; means that the system clears the download archive every seven days.

### <mark style="color:orange;">Enable Auto Reboot</mark>

Go into the `/etc/apt/apt.conf.d` directory and open the `50unattendedupgrades` file in your favorite text editor. In the vicinity of line 68, you'll see a line that says the following:

```
# uncomment
//Unattended-Upgrade::Automatic-Reboot "false";

# change to true
```

With this new configuration, Ubuntu will now reboot itself immediately after the automatic update process has completed. If you'd rather have the machine reboot at a specific time, scroll down to about line 73, where you'll see this line of code:

```
# uncomment and change the time
//Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```



t. To have the machine reboot at 2:00 A.M., just uncomment this line. To have it reboot at, say, 10:00 P.M., uncomment the line and change the time to 22:00, like so:

```
Unattended-Upgrade::Automatic-Reboot-Time "22:00";
```

### <mark style="color:orange;">Testing The Configuration</mark> <a href="#testing-the-configuration" id="testing-the-configuration"></a>

You can perform a dry run to test the configuration. The dry run command runs a test update but no actual changes take place.

You can run the dry run test by using the command:

```
sudo unattended-upgrades --dry-run --debug
```

### <mark style="color:orange;">Disabling Auto Updates</mark>

To disable automatic updates, just go into the `/etc/apt/apt.conf.d` directory and open the `20auto-upgrades` file in your favorite text editor. What you'll see is this:

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

Change the parameter for that second line to 0, so that the file will now look like this:

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "0";
```

Now, the system will still check for updates and show a message at the login screen when any are available, but it won't automatically install them. And of course, it should go without saying that you need to check your systems on a regular basis to see if updates are available. If you do prefer to leave automatic updates enabled, be sure to either enable automatic rebooting or to log in to the system at least a couple of times a week to see if it needs to be rebooted.

## <mark style="color:red;">Updating Redhat7-based Systems</mark>

With Red Hat-based systems, which include CentOS and Oracle Linux, there's no automatic update mechanism that you can set up during installation. So, with the default configuration, you'll need to perform updates yourself:

```
sudo yum upgrade
```

Sometimes, you might just want to see if there are any security-related updates that are ready to be installed. Do that by running the following command:

```
sudo yum updateinfo list updates security
```

If any security updates are available, you'll see them at the end of the command output. On the system that I just tested, there was only one security update available, which looks like this:

```
FEDORA-EPEL-2019-d661b588d2 Low/Sec. nagioscommon-4.4.3-1.el7.x86_64
updateinfo list done
```

. If the only thing you want to install is just the security updates, run the following command:

```
sudo yum upgrade --security
```

### <mark style="color:orange;">CentOS 7 Auto Update</mark>

```
sudo yum install yum-cron
sudo systemctl enable --now yum-cron
```

To configure it, go into the `/etc/yum` directory, and edit the `yum-cron.conf` file. At the top of the file, you'll see this:

```c
[commands]
# What kind of update to use:
# default = yum upgrade
# security = yum --security upgrade
# security-severity:Critical = yum --sec-severity=Critical
upgrade
# minimal = yum --bugfix update-minimal
# minimal-security = yum --security update-minimal
# minimal-security-severity:Critical = --sec-severity=Critical
update-minimal
update_cmd = default
```

This lists the various types of upgrades we can do. The last line shows that we're set to update everything.

Let's say that you only want security updates to get applied automatically. Just change the last line to the following:

```
update_cmd = security
```

On lines 15 and 20, you'll see this line:

```
download_updates = yes
apply_updates = no
```

This indicates that by default, yum-cron is only set to automatically download updates, but not to install them.

If you want the updates to get automatically installed, change the `apply_updates` parameter to `yes`.

{% hint style="info" %}
Note that unlike Ubuntu, there's no setting to make the system automatically reboot itself after an update
{% endhint %}

e, the email\_to = line is set to send messages to the root user account. If you want to receive messages at your own account, just change it here.

```
# Name of the host to connect to to send email messages.
email_host = localhost
```

To see the messages, you'll need to install a mail reader program, if one isn't already installed. (It hasn't been installed if you chose Minimal installation when you installed the operating system.) Your best bet is to install mutt, like so:

```
sudo yum install mutt
```

As with all operating systems, certain updates will require that the system be restarted.&#x20;

And how do you know when the system needs to be restarted? With the needs-restarting command, of course. First, though, you need to make sure that needs-restarting is installed on your system. Do that with the following line of code:

```
/sudo yum install yum-utils
```

Once the package is installed, there are three ways to use needs-restarting. If you just run the command without any option switches, you'll see the services that need to be restarted and the packages that require you to reboot the machine. You can also use the -s or -r options, as shown here:

| Command                  | Explanation                                                                                                  |
| ------------------------ | ------------------------------------------------------------------------------------------------------------ |
| sudo needs-restarting    | This shows the services that need to be restarted, and the reasons why the system might need to be rebooted. |
| sudo needs-restarting -s | This only shows the services that need to be restarted.                                                      |
| sudo needs-restarting -r | This only shows the reasons why the system needs to be rebooted.                                             |

## <mark style="color:red;">Updating Red Hat 8-based systems</mark>

The  difference between yum and dnf is that dnf has a different automatic update mechanism. Instead of installing the `yum-cron` package, you'll now install the `dnf-automatic` package, like so:

```
sudo dnf install dnf-automatic
```

In the `/etc/dnf` directory, you'll see the `automatic.conf` file, which you'll configure the same way as you did the yum-cron.conf file for CentOS 7. Instead of working as a cron job, as the old yum-cron did, `dnf-automatic` works with a systemd timer. When you first install `dnf-automatic`, the timer is disabled. Enable it and start it by running the following line of code:

```
sudo systemctl enable --now dnf-automatic.timer
```

Verify that it's running by typing the following line of code:

```
sudo systemctl status dnf-automatic.timer
```

If it started successfully, you should see something like this:

```
[donnie@redhat-8 ~]$ sudo systemctl status dnf-automatic.timer
dnf-automatic.timer - dnf-automatic timer
Loaded: loaded (/usr/lib/systemd/system/dnf-automatic.timer;
enabled; vendor preset: disabled)
Active: active (waiting) since Sun 2019-07-07 19:17:14 EDT; 13s
ago
Trigger: Sun 2019-07-07 19:54:49 EDT; 37min left
Jul 07 19:17:14 redhat-8 systemd[1]: Started dnf-automatic timer.
[donnie@redhat-8 ~]$
```
