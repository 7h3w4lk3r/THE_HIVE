# Service Management

disable unused services and ports, remove them from startup tasks.

## <mark style="color:red;">systemd & systemctl</mark>

```
# start service
systemctl start smb

# stop service
systemctl stop smb

# restart service
systemctl restart smb

# reload service
systemctl reload smb

# make service launch on boot
systemctl enable smb

# make service not launch on boot
systemctl disable smb

# check status of service (there is alot of nice information here, including latest logs)
systemctl status smb

# NOTE: services are stored in /lib/systemd/system
# NOTE: services that are started on boot, are symlinked from here, to the above folder: /etc/systemd/system/multi-user.target.wants
# NOTE: multi-user.target.wants, represents the default run level
# show services:
systemctl

# no pager
systemctl -a --no-pager

# same way to achieve no pager (pipe to another command, and it will auto remove the pager - "grep ." discards empty lines, showing only lines with text, useful for screen realestate)
systemctl -a | grep .

# show all service files which are installed
systemctl list-unit-files

# if you edit a /lib/systemd/system file (a unit file/ a service file), then you need to reload it into systemd using this, after that you can do things like "restart","start","stop",etc to that service:
systemctl --system daemon-reload

#Viewing Systemd Information
systemctl list-dependencies : Show a unit’s dependencies
systemctl list-sockets : List sockets and what activates
systemctl list-jobs : View active systemd jobs
systemctl list-unit-files : See unit files and their states
systemctl list-units : Show if units are loaded/active
systemctl get – default : List default target (like run level)
```

Looking at logs (All system logs are saved into the journal now, also saved on /var/log/, but in a journal folder which has binary data)

## <mark style="color:red;">Journalctl</mark>

```
# view logs (each line is short)
journalctl

# view logs with normal line lengths (lines are not contracted), get used to always running -a (For all), a for "showing All of the line".
journalctl -a

# to remove pager (note the pager is removed when command is piped to another one)
journalctl -a --no-pager

# or less words (also no pager & skips blank lines - "grep ." shows only lines with text, so empty lines are discarded)
journalctl -a | grep .

# following the log (synonymous with "tail -f /var/log/syslog"), f for "Following"
journalctl -fa

# follow the log, but also show the 1000 lines before now. n for "Number of lines"
journalctl -fan1000

# show the 100 latest lines of logs
journalctl -an100

# viewing a specific service (kind of like grepping, you can achieve same result with grep). assuming atop is the process we want to monitor
journalctl -a  _SYSTEMD_UNIT=atop.service

# can achieve similar results with
journalctl -a | grep atop
```

{% embed url="http://patrakov.blogspot.com/2011/01/writing-systemd-service-files.html" %}

{% embed url="https://coreos.com/docs/launching-containers/launching/getting-started-with-systemd" %}

















