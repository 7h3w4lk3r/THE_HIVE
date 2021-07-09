---
description: >-
  Since this is not a complete Linux tutorial I'm not going to explain every
  little detail of Linux OS architecture and administration, just some tips for
  hardening your OS.
---

# Linux Security Hardening

Since there are so many checklists, standards and guides for operating system hardening out there, i tried to make this as short as possible so i wont need to read through 200 pages of some manual every time i want to configure a system.

#### This checklist is a combination of my own experience plus the methods and techniques included in guides like CIS benchmarks and some other resources on the web. the target in most of these items is Ubuntu/Debian based distros but most of them are applicable on RedHat/CentOS as well. this is by no means an all-in-one solution and i highly appreciate any comments, adages and rants you might have :\)



## File system

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













 

