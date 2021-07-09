---
description: >-
  Since this is not a complete Linux tutorial I'm not going to explain every
  little detail of Linux OS architecture and administration, just some tips for
  hardening your OS.
---

# Linux Security Hardening

Since there are so many checklists, standards and guides for operating system hardening out there, i tried to make this as short as possible so i wont need to read through 200 pages of some manual every time i want to configure a system.

#### This checklist is a combination of my own experience plus the methods and techniques included in guides like CIS benchmarks and some other resources on the web. the target distro in most of these items is Ubuntu/Debian based distros but most of them are applicable on RedHat/CentOS as well. this is by no means an all-in-one solution and i highly appreciate any comments, adages and rants you might have :\)



## File System Security

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>tasks/configurations</b>
      </th>
      <th style="text-align:left"><b>methods/commands</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>separate partitions while installing the system (prevention from local attacks, starvation and privilege escalation)</b>
      </td>
      <td style="text-align:left">
        <p>/boot</p>
        <p>/home</p>
        <p>/usr</p>
        <p>/var</p>
        <p>/tmp</p>
        <p>/var/tmp</p>
        <p>/var/log</p>
        <p>/var/log/audit</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>nodev option set on /tmp</b>
      </td>
      <td style="text-align:left">
        <p>Edit the /etc/fstab file and add nodev to the fourth field (mounting options)
          for the /tmp partition.</p>
        <p>See the fstab(5) manual page for more information.</p>
        <p></p>
        <p>Run the following command to remount /tmp :</p>
        <p></p>
        <p># mount -o remount,nodev /tmp</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>nosuid option set on /tmp partition</b>
      </td>
      <td style="text-align:left">
        <p>If a /tmp partition exists run the following command and verify that the
          nosuid option is set on /tmp:</p>
        <p></p>
        <p># mount | grep /tmp</p>
        <p></p>
        <p>Edit the /etc/fstab file and add nosuid to the fourth field (mounting
          options) for the /tmp partition Run the following command to remount /tmp
          :</p>
        <p></p>
        <p>#mount -o remount,nosuid /tmp</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>nodev option set on /var/tmp partition</b>
      </td>
      <td style="text-align:left">
        <p>mount | grep /var/tmp Edit the /etc/fstab file and add nodev to the fourth
          field</p>
        <p></p>
        <p>#mount -o remount,nodev /var/tmp</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>nosuid option set on /var/tmp partition</b>
      </td>
      <td style="text-align:left">
        <p>mount | grep /var/tmp Edit the /etc/fstab file and add nosuid to the fourth
          field:</p>
        <p></p>
        <p># mount -o remount,nosuid /var/tmp</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>noexec option set on /var/tmp partition</b>
      </td>
      <td style="text-align:left">
        <p>mount | grep /var/tmp Edit the /etc/fstab file and add noexec to the fourth
          field:</p>
        <p></p>
        <p># mount -o remount,noexec /var/tmp</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>nodev option set on /home partition</b>
      </td>
      <td style="text-align:left">
        <p>mount | grep /home Edit the /etc/fstab file and add nodev to the fourth
          field:</p>
        <p></p>
        <p># mount -o remount,nodev /home</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>nodev option set on /dev/shm partition</b>
      </td>
      <td style="text-align:left">
        <p>mount | grep /dev/shm Edit the /etc/fstab file and add nodev to the fourth
          field:</p>
        <p></p>
        <p># mount -o remount,nodev /dev/shm</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>nosuid option set on /dev/shm partition</b>
      </td>
      <td style="text-align:left">
        <p>mount | grep /dev/shm Edit the /etc/fstab file and add nosuid to the fourth
          field:</p>
        <p></p>
        <p># mount -o remount,nosuid /dev/shm</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>noexec option set on /dev/shm partition</b>
      </td>
      <td style="text-align:left">
        <p>mount | grep /dev/shm</p>
        <p></p>
        <p>Edit the /etc/fstab file and add noexec to the fourth field:</p>
        <p></p>
        <p>#mount -o remount,noexec /dev/shm</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>nodev option set on removable media partitions</b>
      </td>
      <td style="text-align:left">Run the following command and verify that the nodev option is set on all
        removable media partitions mount Edit the /etc/fstab file and add nodev
        to the fourth field</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>nosuid option set on removable media partitions</b>
      </td>
      <td style="text-align:left">Run the following command and verify that the nosuid option is set on
        all removable media partitions mount Edit the /etc/fstab file and add nosuid
        to the fourth field</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>noexec option set on removable media partitions</b>
      </td>
      <td style="text-align:left">Run the following command and verify that the noexec option is set on
        all removable media partitions mount Edit the /etc/fstab file and add noexec
        to the fourth field</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>sticky bit is set on all world-writable directories (prevents users from deleting or renaming files in that directory that are not owned by them)</b>
      </td>
      <td style="text-align:left">
        <p>Run the following command to verify no world writable directories exist
          without the sticky bit set:</p>
        <p></p>
        <p># df --local -P | awk {&apos;if (NR!=1) print $6&apos;} | xargs -I &apos;{}&apos;
          find &apos;{}&apos; -xdev -type d \( -perm -0002 -a ! -perm -1000 \) 2&gt;/dev/null</p>
        <p></p>
        <p>No output should be returned Run the following command to set the sticky
          bit on all world writable directories:</p>
        <p></p>
        <p>df --local -P | awk {&apos;if (NR!=1) print $6&apos;} | xargs -I &apos;{}&apos;
          find &apos;{}&apos; -xdev -type d -perm -0002 2&gt;/dev/null | xargs chmod
          a+t</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Disable Auto mounting (prevent physical access attacks, not for workstation users)</b>
      </td>
      <td style="text-align:left">
        <p>Run the following command to verify autofs is not enabled:</p>
        <p></p>
        <p># systemctl is-enabled autofs</p>
        <p></p>
        <p>Verify result is not &quot;enabled&quot;. to disable autofs :</p>
        <p></p>
        <p>systemctl disable autofs</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>mount options</b>
      </td>
      <td style="text-align:left">
        <p>nodev : no device access nosuid : no setuid</p>
        <p></p>
        <p>programs noexec : prevent running exec</p>
        <p></p>
        <p>--remount, noexec : mount again with no exec</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p><b>Filesystem Integrity Checking while initializing the data base aide will take an snapshot of all system libraries and binaries for future compare to see if anything changed or not copy the new created db in /var/lib/aide aide.db.new.gz to aide.db.gz do a check for all files for mismatches: </b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p><b># aide --check</b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p><b> if there was a change we will see a warning Updating the aide DB after checking the aide db: </b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p><b>aide -c aide.conf --update </b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p><b>This will reset the database to the current status of the /etc/ sub-directory.</b>
        </p>
      </td>
      <td style="text-align:left">
        <p>Ensure AIDE is installed:</p>
        <p></p>
        <p># dpkg -s aide install aide: # apt-get install aide aide-common initialize
          aide:</p>
        <p></p>
        <p># aideinit</p>
        <p></p>
        <p>conf file: /etc/aide.conf</p>
        <p>log file: /etc/log/aide db:</p>
        <p>/etc/lib/aide</p>
        <p></p>
        <p>determine if there is a cron job scheduled to run the aide check: #</p>
        <p></p>
        <p>crontab -u root -l | grep aide</p>
        <p># grep -r aide /etc/cron.* /etc/crontab</p>
        <p></p>
        <p>Run the following command:</p>
        <p></p>
        <p># crontab -u root -e</p>
        <p></p>
        <p>Add the following line to the crontab:</p>
        <p></p>
        <p>0 5 * * * /usr/bin/aide.wrapper --config /etc/aide/aide.conf --check</p>
      </td>
    </tr>
  </tbody>
</table>



## OS Security

<table>
  <thead>
    <tr>
      <th style="text-align:left">tasks/configurations</th>
      <th style="text-align:left">methods/commands</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>verify package repositories</b>
      </td>
      <td style="text-align:left">apt-cache policy</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Ensure GPG keys are configured</b>
      </td>
      <td style="text-align:left">apt-key list</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>text console messages / warning banners</b>
      </td>
      <td style="text-align:left">/etc/issue : before login /etc/motd : after login</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>disable Ctrl+Alt+Del</b>
      </td>
      <td style="text-align:left">systemctl mask ctrl+alt+del.target</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>update strategy</b>
      </td>
      <td style="text-align:left">RHEL : updateinfo yum install yum-plugin-security Debian : apt-get -s
        dist-upgrade | grep &quot;^Inst&quot; | awk -F &quot; &quot; &apos;{ print
        $2 }&apos; | xargs apt-get install unattended-upgrades</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>auto update</b>
      </td>
      <td style="text-align:left">setup auto-update for RHEL &amp; Debian</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>validate packages</b>
      </td>
      <td style="text-align:left">
        <p>RHEL :</p>
        <p>rpm -Va</p>
        <p></p>
        <p>Debian :</p>
        <p>debsums -l &gt;&gt;&gt; generate a list</p>
        <p>debsums -c &gt;&gt;&gt; check all</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>bootloader security</b>
      </td>
      <td style="text-align:left">
        <p>check if grub is password protected:</p>
        <p></p>
        <p>#grep &quot;^set superusers&quot; /boot/grub/grub.cfg grep &quot;^password&quot;
          /boot/grub/grub.cfg</p>
        <p></p>
        <p>Create an encrypted password with grub-mkpasswd-pbkdf2 :</p>
        <p></p>
        <p># grub-mkpasswd-pbkdf2 Add the following into /etc/grub.d/00_header or
          a custom /etc/grub.d configuration file:</p>
        <p></p>
        <p>``` cat &lt;&lt;EOF set superusers=&quot;&lt;username&gt;&quot; password_pbkdf2
          &lt;username&gt; &lt;encrypted-password&gt; EOF ```</p>
        <p></p>
        <p>Run the following command to update the grub2 configuration:</p>
        <p></p>
        <p># update-grub</p>
        <p></p>
        <p>Run the following command and verify Uid and Gid are both 0/root and Access
          does not grant permissions to group or other :</p>
        <p></p>
        <p># stat /boot/grub/grub.cfg</p>
        <p></p>
        <p>Run the following commands to set permissions on your grub configuration:</p>
        <p></p>
        <p># chown root:root /boot/grub/grub.cfg</p>
        <p># chmod og-rwx /boot/grub/grub.cfg</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>core dumps restrictions (prevents users from overriding the soft variable and confidential information leak)</b>
      </td>
      <td style="text-align:left">
        <p>Run the following commands and verify output matches:</p>
        <p></p>
        <p># grep &quot;hard core&quot; /etc/security/limits.conf /etc/security/limits.d/*
          * hard core 0</p>
        <p></p>
        <p># sysctl fs.suid_dumpable fs.suid_dumpable = 0</p>
        <p></p>
        <p># grep &quot;fs\.suid_dumpable&quot; /etc/sysctl.conf /etc/sysctl.d/*
          fs.suid_dumpable = 0</p>
        <p></p>
        <p>Add the following line to /etc/security/limits.conf or a /etc/security/limits.d/*
          file:</p>
        <p></p>
        <p>* hard core 0</p>
        <p></p>
        <p>Set the following parameter in /etc/sysctl.conf or a /etc/sysctl.d/* file:</p>
        <p></p>
        <p>fs.suid_dumpable = 0</p>
        <p></p>
        <p>Run the following command to set the active kernel parameter:</p>
        <p></p>
        <p># sysctl -w fs.suid_dumpable=0</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>enable XD/NX support (no execute / execute disabl, Enabling any feature that can protect against buffer overflow attacks enhances the security of the system.)</b>
      </td>
      <td style="text-align:left">
        <p>Run the following command and verify your kernel has identified and activated
          NX/XD protection:</p>
        <p></p>
        <p># dmesg | grep NX NX (Execute Disable) protection: active</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>enable address space layout randomization (ASLR)</b>
      </td>
      <td style="text-align:left">
        <p>Run the following command and verify output matches</p>
        <p></p>
        <p># sysctl kernel.randomize_va_space kernel.randomize_va_space = 2</p>
        <p></p>
        <p># grep &quot;kernel\.randomize_va_space&quot; /etc/sysctl.conf /etc/sysctl.d/*
          kernel.randomize_va_space = 2</p>
        <p></p>
        <p>Set the following parameter in /etc/sysctl.conf or a /etc/sysctl.d/* file:</p>
        <p></p>
        <p>kernel.randomize_va_space = 2</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>disable prelink (prelinking feature can interfere with the operation of AIDE, because it changes binaries.)</b>
      </td>
      <td style="text-align:left">
        <p>verify prelink is not installed:</p>
        <p></p>
        <p># dpkg -s prelink</p>
        <p></p>
        <p>restore binaries to normal:</p>
        <p></p>
        <p># prelink -ua uninstall prelink :</p>
        <p></p>
        <p># apt-get remove prelink</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>enable SELinux in bootloader configurations</b>
      </td>
      <td style="text-align:left">
        <p>verify that no linux line has the selinux=0 or enforcing=0 parameters
          set:</p>
        <p></p>
        <p># grep &quot;^\s*linux&quot; /boot/grub/grub.cfg</p>
        <p></p>
        <p>Edit /etc/default/grub and remove all instances of selinux=0 and enforcing=0
          from all CMDLINE_LINUX parameters:</p>
        <p></p>
        <p>GRUB_CMDLINE_LINUX_DEFAULT=&quot;quiet&quot; GRUB_CMDLINE_LINUX=&quot;&quot;</p>
        <p></p>
        <p>update the grub2 configuration:</p>
        <p></p>
        <p># update-grub</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>set SELinux enforsing state</b>
      </td>
      <td style="text-align:left">
        <p>ensure output matches:</p>
        <p></p>
        <p># grep SELINUX=enforcing /etc/selinux/config</p>
        <p>SELINUX=enforcing</p>
        <p></p>
        <p># sestatus</p>
        <p>SELinux status: enabled</p>
        <p>Current mode: enforcing</p>
        <p>Mode from config file: enforcing</p>
        <p></p>
        <p>Edit the /etc/selinux/config file to set the SELINUX parameter:</p>
        <p></p>
        <p>SELINUX=enforcing</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Configure AppArmor</b>
      </td>
      <td style="text-align:left">http://wiki.apparmor.net/index.php/Documentation</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>enable apparmor in bootloader</b>
      </td>
      <td style="text-align:left">
        <p>verify that no linux line the apparmor=0 parameter set:</p>
        <p></p>
        <p># grep &quot;^\s*linux&quot; /boot/grub/grub.cfg</p>
        <p></p>
        <p>Edit /etc/default/grub and remove all instances of apparmor=0 from all
          CMDLINE_LINUX parameters:</p>
        <p></p>
        <p>GRUB_CMDLINE_LINUX_DEFAULT=&quot;quiet&quot; GRUB_CMDLINE_LINUX=&quot;&quot;</p>
        <p></p>
        <p>update the grub2 configuration:</p>
        <p></p>
        <p># update-grub</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>set all AppArmor profiles to enforcing</b>
      </td>
      <td style="text-align:left">
        <p>Run the following command and verify that profiles are loaded, no profiles
          are in complain mode, and no processes are unconfined:</p>
        <p></p>
        <p># apparmor_status</p>
        <p></p>
        <p>set all profiles to enforce mode:</p>
        <p></p>
        <p># aa-enforce /etc/apparmor.d/*</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>scan kernel parameters with Lynis</b>
      </td>
      <td style="text-align:left">
        <p># apt install lynis</p>
        <p># lynis audit system</p>
        <p></p>
        <p>Use grep to send all of the DIFFERENT lines to a new file. Name it 60-
          secure_values.conf:</p>
        <p></p>
        <p># grep &quot;DIFFERENT&quot; secure_values,conf &gt; 60-secure_values.conf</p>
        <p></p>
        <p>Edit the 60-secure_values.conf file to convert it into the sysctl configuration
          format.</p>
        <p></p>
        <p>Set each parameter to the exp value that&apos;s currently within the pairs
          of parentheses. The finished product should look something like this:</p>
        <p></p>
        <p>``` kernel.dmesg_restrict = 1 kernel.kptr_restrict = 2 kernel.sysrq =
          0 kernel.yama.ptrace_scope = 1 2 3 net.ipv4.conf.all.accept_redirects =
          0 net.ipv4.conf.all.log_martians = 1 net.ipv4.conf.all.send_redirects =
          0 net.ipv4.conf.default.accept_redirects = 0 net.ipv4.conf.default.log_martians
          = 1 net.ipv6.conf.all.accept_redirects = 0 net.ipv6.conf.default.accept_redirects
          = 0 ```</p>
        <p></p>
        <p></p>
        <p>Copy the file to the /etc/sysctl.d directory:</p>
        <p></p>
        <p># sudo cp 60-secure_values.conf /etc/sysctl.d/</p>
        <p></p>
        <p>Reboot the machine to read in the values from the new file:</p>
        <p></p>
        <p># sudo shutdown -r now</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>process isolation (prevent users from seeing each others processes)</b>
      </td>
      <td style="text-align:left">
        <p>mount the /proc filesystem with the hidepid option.</p>
        <p>You can do this by adding the following line to the end of the /etc/fstab
          file, like so:</p>
        <p></p>
        <p># nano /etc/fstab add:</p>
        <p></p>
        <p>``` proc /proc proc hidepid=2 0 0 ```</p>
        <p></p>
        <p>Then, remount /proc , like so:</p>
        <p></p>
        <p>#mount -o remount proc</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>resource limits on processes</b>
      </td>
      <td style="text-align:left">
        <p>could do that by hand-editing the systemd service file for each service
          that we want to limit, but it&apos;s easier to just run a systemctl command,
          like so for example for apache service:</p>
        <p></p>
        <p># sudo systemctl set-property apache2.service MemoryAccounting=1 CPUAccounting=1
          BlockIOAccounting=1</p>
        <p></p>
        <p>now there is a directory created for apache service in /etc/systemd/system.control
          named apache2.service.d which contains files that turn our accounting functions
          on.</p>
        <p>inside each file we see 2 lines modify the original apache2.service file
          in order to turn on accounting:</p>
        <p></p>
        <p>``` CPUAccounting=yes ```</p>
        <p></p>
        <p>to set resource limits for each account type:</p>
        <p></p>
        <p># systemctl set-property apache2.service CPUQouta=40% MemoryLimit=500M</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>resource limits on user accounts</b>
      </td>
      <td style="text-align:left">
        <p># systemctl set-property user-&lt;UID&gt;.slice MemoryAccounting=1 CPUAccounting=1
          BlockIOAccounting=1</p>
        <p></p>
        <p>#systemctl set-property user-&lt;UID&gt;.slice CPUQouta=20% MemoryLimit=500M</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>sandbox untrusted applications using firejail (uses namespaces, SECCOMP, and kernel capabilities to run untrusted applications in their own individual sandboxes)</b>
      </td>
      <td style="text-align:left">
        <p>installation:</p>
        <p></p>
        <p># apt install firejail</p>
        <p></p>
        <p>usage:</p>
        <p></p>
        <p># firejail &lt;application&gt;</p>
        <p></p>
        <p># firejail firefox</p>
        <p></p>
        <p>run a sandbox with dropped capabilities:</p>
        <p></p>
        <p># firejail --caps.drop=all chromium-browser</p>
        <p></p>
        <p>to create a symbolic link for each program:</p>
        <p></p>
        <p># firecfg</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>sandboxing using Snappy</b>
      </td>
      <td style="text-align:left">
        <p>install: # apt install snapd search for apps online to run:</p>
        <p></p>
        <p># snap search &lt;app&gt;</p>
        <p></p>
        <p>install the app:</p>
        <p></p>
        <p># snap install &lt;app&gt;</p>
        <p></p>
        <p>create an account at ubuntu.com and login in snap with your credentials:</p>
        <p></p>
        <p># snap login</p>
        <p></p>
        <p>start the app in sandbox:</p>
        <p></p>
        <p># snap start</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>disable unneeded debugging/testing services: daytime chargen discard echo time rsh telnet xinetd</b>
      </td>
      <td style="text-align:left">Comment out or remove any lines starting with chargen from /etc/inetd.conf
        and /etc/inetd.d/* . Set disable = yes on all chargen services in /etc/xinetd.conf
        and /etc/xinetd.d/* . Comment out or remove any lines starting with daytime
        from /etc/inetd.conf and /etc/inetd.d/* . Set disable = yes on all daytime
        services in /etc/xinetd.conf and /etc/xinetd.d/* . Comment out or remove
        any lines starting with discard from /etc/inetd.conf and /etc/inetd.d/*
        . Set disable = yes on all discard services in /etc/xinetd.conf and /etc
        xinetd.d/* . Comment out or remove any lines starting with echo from /etc/inetd.conf
        and /etc/inetd.d/* . Set disable = yes on all echo services in /etc/xinetd.conf
        and /etc xinetd.d/* . Comment out or remove any lines starting with time
        from /etc/inetd.conf and /etc/inetd.d/* . Set disable = yes on all time
        services in /etc/xinetd.conf and /etc/xinetd.d/* . Verify the rsh services
        are not enabled. Run the following commands and verify results are as indicated:
        grep -R &quot;^shell&quot; /etc/inetd.* grep -R &quot;^login&quot; /etc/inetd.*
        grep -R &quot;^exec&quot; /etc/inetd.* No results should be returned check
        /etc/xinetd.conf and /etc/xinetd.d/* and verify all rsh , rlogin , and
        rexec services have disable = yes set. Comment out or remove any lines
        starting with shell , login , or exec from /etc/inetd.conf and /etc/inetd.d/*
        . Set disable = yes on all rsh , rlogin , and rexec services in /etc/xinetd.conf
        and /etc/xinetd.d/* . Verify the telnet service is not enabled. Run the
        following command and verify results are as indicated: # grep -R &quot;^telnet&quot;
        /etc/inetd.* check /etc/xinetd.conf and /etc/xinetd.d/* and verify all
        telnet services have disable = yes set. Comment out or remove any lines
        starting with telnet from /etc/inetd.conf and /etc/inetd.d/* . Set disable
        = yes on all telnet services in /etc/xinetd.conf and /etc xinetd.d/* .
        Run the following command to verify xinetd is not enabled: # systemctl
        is-enabled xinetd disabled Run the following command to disable xinetd
        : # systemctl disable xinetd</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>enable time synchronization and configure ntp service</b>
      </td>
      <td style="text-align:left">On physical systems or virtual systems where host based time synchronization
        is not available run the following commands and verify either NTP or chrony
        is installed: # dpkg -s ntp # dpkg -s chrony On physical systems or virtual
        systems where host based time synchronization is not available install
        NTP or chrony using one of the following commands: # apt-get install ntp
        # apt-get install chrony Run the following command and verify output matches:
        # grep &quot;^restrict&quot; /etc/ntp.conf restrict -4 default kod nomodify
        notrap nopeer noquery restrict -6 default kod nomodify notrap nopeer noquery
        Run the following command and verify remote server is configured properly:
        # egrep &quot;^(server|pool)&quot; /etc/ntp.conf server &lt;remote-server&gt;
        Multiple servers may be configured. Verify that ntp is configured to run
        as the ntp user by running the following command: # grep &quot;RUNASUSER=ntp&quot;
        /etc/init.d/ntp RUNASUSER=ntp Add or edit restrict lines in /etc/ntp.conf
        to match the following: restrict -4 default kod nomodify notrap nopeer
        noquery restrict -6 default kod nomodify notrap nopeer noquery Add or edit
        server or pool lines to /etc/ntp.conf as appropriate: server &lt;remote-server&gt;
        Configure ntp to run as the ntp user by adding or editing the /etc/init.d
        ntp file: RUNASUSER=ntp</td>
    </tr>
  </tbody>
</table>









 

