# Insecure GUI APPs

On some \(older\) versions of Windows, users could be granted the permission to run certain GUI apps with administrator privileges. There are often numerous ways to spawn command prompts from within GUI apps, including using native Windows functionality. Since the parent process is running with administrator privileges, the spawned command prompt will also run with these privileges. I call this the “Citrix Method” because it uses many of the same techniques used to break out of Citrix environments.

#### for the demo we use the old version of windows paint app running as admin.

Open a command prompt and run:

```text
tasklist /V | findstr mspaint.exe
```

![](../../../../.gitbook/assets/image%20%28164%29.png)

Note that mspaint.exe is running with admin privileges.

In Paint, click File, then Open.In the navigation input, replace the contents with:

```text
file://c:/windows/system32/cmd.exe
```

ress Enter. A command prompt should open running with admin privileges.

![](../../../../.gitbook/assets/image%20%28166%29.png)

as you can see we have a cmd shell with admin privileges by opening the cmd.exe file from a vulnerable GUI app.



