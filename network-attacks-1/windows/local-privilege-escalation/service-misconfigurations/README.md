# Service Misconfigurations

Services are simply programs that run in the background, accepting input or performing regular tasks. If services run with SYSTEM privileges and are misconfigured, exploiting them may lead to command execution with SYSTEM privileges as well

## <mark style="color:red;">Service Commands</mark>

Query the configuration of a service:

```
sc.exe qc <name>
```

Query the current status of a service:

```
sc.exe query <name>
```

Modify a configuration option of a service:

```
sc.exe config <name> <option>= <value>
```

​​Start/Stop a service:

```
net start/stop <name>
```

## <mark style="color:red;">Service Misconfigurations</mark>

1. <mark style="color:green;">Insecure Service Properties</mark>
2. <mark style="color:green;">Unquoted Service Path</mark>
3. <mark style="color:green;">Weak Registry Permissions</mark>
4. <mark style="color:green;">Insecure Service Executables</mark>
5. <mark style="color:green;">Service DLL Hijacking</mark>
