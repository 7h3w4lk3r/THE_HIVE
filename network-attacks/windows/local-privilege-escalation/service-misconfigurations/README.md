# Service Misconfigurations

Services are simply programs that run in the background, accepting input or performing regular tasks. If services run with SYSTEM privileges and are misconfigured, exploiting them may lead to command execution with SYSTEM privileges as well

### Service Commands

Query the configuration of a service:

```text
sc.exe qc <name>
```

Query the current status of a service:

```text
sc.exe query <name>
```

Modify a configuration option of a service:

```text
sc.exe config <name> <option>= <value>
```

​​Start/Stop a service:

```text
net start/stop <name>
```

#### Service Misconfigurations

1. Insecure Service Properties
2. Unquoted Service Path
3. Weak Registry Permissions
4. Insecure Service Executables
5. DLL Hijacking



