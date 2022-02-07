# Service Management

## <mark style="color:red;">Overview of Linux Components</mark>

{% hint style="danger" %}
**disable unused services and ports, remove them from startup tasks.**
{% endhint %}

![](<../../../.gitbook/assets/image (285).png>)

## <mark style="color:red;">Cheat sheet</mark>

#### <mark style="color:green;">Systemd Service Commands</mark>

| Command                                  | Description                          |
| ---------------------------------------- | ------------------------------------ |
| systemctl stop service-name              | systemd stop running service         |
| systemctl start service-name             | systemctl start service              |
| systemctl restart service-name           | systemd restart running service      |
| systemctl reload service-name            | reloads all config files for service |
| systemctl status service-name            | systemctl show if service is running |
| systemctl enable service-name            | systemctl start service at boot      |
| systemctrl disable service-name          | systemctl - disable service at boot  |
| systemctl show service-name              | show systemctl service info          |
| systemctl -H target command service-name | run systemctl commands remotely      |

#### <mark style="color:green;">Systemd Information Commands</mark>

\
Systemd commands that show useful system information.\\

| Command                     | Description                                    |
| --------------------------- | ---------------------------------------------- |
| systemctl list-dependencies | show and units dependencies                    |
| systemctl list-sockets      | systemd list sockets and activities            |
| systemctl list-jobs         | view active systemd jobs                       |
| systemctl list-unit-files   | systemctl list unit files and their states     |
| systemctl list-units        | systemctl list default target (like run level) |

#### <mark style="color:green;">Changing System State</mark>

\
systemd reboot, shutdown, default target etc

| Command             | Description                               |
| ------------------- | ----------------------------------------- |
| systemctl reboot    | systemctl reboot the system               |
| systemctl poweroff  | systemctl shutdown (power off the system) |
| systemctl emergency | Put in emergency mode                     |
| systemctl default   | systemctl default mode                    |

\
<mark style="color:green;">Systemctl Viewing Log Messages</mark>

| Command                    | Description                     |
| -------------------------- | ------------------------------- |
| journalctl                 | show all collected log messages |
| journalctl -u sshd.service | see sshd service messages       |
| journelctl -f              | follow messages as they appear  |
| journelctl -k              | show kernel messages only       |

## <mark style="color:red;">systemctl</mark>

Running systemctl without any arguments invokes the default list-units subcom- mand, which shows all loaded and active services, sockets, targets, mounts, and devices.

```
systemctl
```

![](<../../../.gitbook/assets/image (296) (1).png>)

## <mark style="color:red;">Unit statuses</mark>

The last four lines are recent log entries. **By default, the log entries are condensed so that each entry takes only one line.** This compression often makes entries un- readable, so we included the -l option to request full entries. It makes no difference in this case, but it’s a useful habit to acquire

![](<../../../.gitbook/assets/image (283).png>)

Many units have no installation procedure, so they can’t truly be said to be enabled or disabled; they’re just available. Such units’ status is listed as static . They only become active if activated by hand (systemctl start) or named as a dependency of other active units.

**Unit files that are linked were created with systemctl link**. **This command creates a symbolic link from one of systemd’s system directories to a unit file that lives else- where in the filesystem.** Such unit files can be addressed by commands or named as dependencies, but they are not full citizens of the ecosystem and have some no- table quirks. For example, running systemctl disable on a linked unit file deletes the link and all references to it

The <mark style="color:orange;">masked status</mark> means “administratively blocked.” systemd knows about the unit, but has been forbidden from activating it or acting on any of its configuration direc- tives by systemctl mask. As a rule of thumb, turn off units whose status is enabled or linked with systemctl disable and reserve systemctl mask for static units.

## <mark style="color:red;">Targets</mark>

systemd does define a distinct class of units (of type .target) to act as well-known markers for common operating modes. However, targets have no real superpowers beyond the dependency management that’s available to any other unit.

The only targets to really be aware of are multi-user.target and graphical.target for day-to-day use, and rescue.target for accessing single-user mode. To change the system’s current operating mode, use the systemctl isolate command:

```
sudo systemctl isolate multi-user.target
```

The isolate subcommand is so-named because it activates the stated target and its dependencies but deactivates all other units.

![](<../../../.gitbook/assets/image (282).png>)

To see the target the system boots into by default:

```
 systemctl get-default
```

### <mark style="color:orange;">Set Default Target</mark>

```
 sudo systemctl set-default multi-user.target
```

### <mark style="color:orange;">see all the system’s available targets</mark>

```
systemctl list-units --type=target 
```

### <mark style="color:orange;">Dependencies among units</mark>

Individual units can turn off these assumptions with the line:

`DefaultDependencies=false`

in the \[Unit] section of their unit file; the default is true . See the man page for systemd.unit-type to see the exact assumptions that apply to each type of unit (e.g., man systemd.service).

![](<../../../.gitbook/assets/image (284).png>)

all the options in the table express the basic idea that the unit being configured depends on some set of other units. The exact dis- tinctions among these options are subtle and primarily of interest to service devel- opers. The least restrictive variant, Wants , is preferred when possible.

You can extend a unit’s Wants or Requires cohorts by creating a unit-file.wants or unit-file.requires directory in /etc/systemd/system and adding symlinks there to other unit files. Better yet, just let systemctl do it for you.

### <mark style="color:orange;">add dependency to a service</mark>

add my.local.service as a dependency to multi-user.target so when ever the system is in multi-user mode this service will be started

```
sudo systemctl add-wants multi-user.target my.local.service
```

### <mark style="color:orange;">Execution order</mark>

When the system transitions to a new state, systemd first traces the various sources of dependency information outlined in the previous section to identify the units that will be affected. It then uses Before and After clauses from the unit files to sort the work list appropriately. To the extent that units have no Before or After constraints, they are free to be adjusted in parallel.

### <mark style="color:orange;">Local services and customizations</mark>

Browse the examples in `/usr/lib/systemd/system` and adapt one that’s close to what you want. See the man page for systemd.service for a complete list of configuration options for services. For options common to all types of units, see the page for systemd.unit. Put your new unit file in `/etc/systemd/system`. You can then run

```
sudo systemctl enable custom.service
```

to activate the dependencies listed in the service file’s \[Install] section

<mark style="color:orange;">As a general rule, you should never edit a unit file you didn’t write. Instead, create a configuration directory in</mark> <mark style="color:orange;">`/etc/systemd/system/unit-file.d`</mark> <mark style="color:orange;">and add one or more configuration files there called xxx.conf</mark>. The xxx part doesn’t matter; just make sure the file has a .conf suffix and is in the right location. override.conf is the standard name..conf files have the same format as unit files, and in fact systemd smooshes them all together with the original unit file. However, override files have priority over the original unit file should both sources try to set the value of a particular option.many systemd options are allowed to appear more than once in a unit file. In these cases, the multiple values form a list and are all active simultaneously. If you assign a value in your override.conf file, that val- ue joins the list but does not replace the existing entries. This may or may not be what you want. To remove the existing entries from a list, just assign the option an empty value before adding your own.

## <mark style="color:red;">Managing Services</mark>

### <mark style="color:orange;">show only loaded and active services</mark>

```
systemctl list-units --type=service

 systemctl -t service --state=active 
 systemctl list-unit-files --state=enabled
```

all the installed unit files, regardless of whether or not they’re active:

```
systemctl list-unit-files --type=service
```

### <mark style="color:orange;">enable/disable boot launch</mark>

```
systemctl enable/disable smb
```

### <mark style="color:orange;">check status of service</mark>

(there is alot of nice information here, including latest logs) systemctl status smb

```
systemctl status smb
```

{% hint style="info" %}
NOTE: services are stored in /lib/systemd/system

services that are started on boot, are symlinked from here, to the above folder: /etc/systemd/system/multi-user.target.wants

multi-user.target.wants, represents the default run level
{% endhint %}

### <mark style="color:orange;">no pager</mark>

```
systemctl -a --no-pager
```

same way to achieve no pager (pipe to another command, and it will auto remove the pager - "grep ." discards empty lines, showing only lines with text, useful for screen realestate)

```
systemctl -a | grep .
```

### <mark style="color:orange;">show all service files which are installed</mark>

```
systemctl list-unit-files
```

if you edit a `/lib/systemd/system` file (a unit file/ a service file), then you need to reload it into systemd using this, after that you can do things like "restart","start","stop",etc to that service:

```
systemctl --system daemon-reload
```

### <mark style="color:orange;">Viewing Systemd Information</mark>

```
// Some cosystemctl list-dependencies : Show a unit’s dependencies
systemctl list-sockets : List sockets and what activates
systemctl list-jobs : View active systemd jobs
systemctl list-unit-files : See unit files and their states
systemctl list-units : Show if units are loaded/active
systemctl get – default : List default target (like run level)
```

### <mark style="color:orange;">manage temp files (/tmp)</mark>

```
 systemd-tmpfiles
```

## <mark style="color:red;">Create a Systemd Service</mark>

### <mark style="color:orange;">common unit file paths</mark>

```
/usr/lib/systemd/system
/lib/systemd/system
/etc/systemd/system
/run/systemd/systemcreating a unit file
```

### <mark style="color:orange;">creating a unit file</mark>

```
cd /etc/systemd/system
```

Create a file named your-service.service and include the following:

```
[Unit]
Description=<description about this service>

[Service]
User=<user e.g. root>
WorkingDirectory=<directory_of_script e.g. /root>
ExecStart=<script which needs to be executed>
Restart=always

[Install]
WantedBy=multi-user.target
```

For Python specific projects which include virtual environment:

```
[Unit]
Description=<project description>

[Service]
User=<user e.g. root>
WorkingDirectory=<path to your project directory containing your python script>
ExecStart=/home/user/.virtualenv/bin/python main.py
Restart=always
# replace /home/user/.virtualenv/bin/python with your virtualenv and main.py with your script

[Install]
WantedBy=multi-user.target
```

OR

```
[Unit]
Description=<project description>

[Service]
User=<user e.g. root>
WorkingDirectory=<path to your project directory>
ExecStart=/bin/bash -c 'cd /home/ubuntu/project/ && source venv/bin/activate && python test.py'

[Install]
WantedBy=multi-user.target
```

### <mark style="color:orange;">activate and start the new service</mark>

Reload the service files to include the new service.

```
sudo systemctl daemon-reload
```

Start your service

```
sudo systemctl start your-service.service
```

To check the status of your service

```
sudo systemctl status example.service
```

To enable your service on every reboot

```
sudo systemctl enable example.service
```

To disable your service on every reboot

```
sudo systemctl disable example.service
```

## <mark style="color:red;">Journalctl</mark>

Looking at logs (All system logs are saved into the journal now, also saved on /var/log/, but in a journal folder which has binary data)

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
