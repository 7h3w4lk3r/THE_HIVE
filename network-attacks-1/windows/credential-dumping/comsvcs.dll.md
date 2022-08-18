# comsvcs.dll

## <mark style="color:red;">Dumping LSASS with comsvcs.dll</mark>

#### <mark style="color:green;">There’s a DLL called comsvcs.dll, located in C:\Windows\System32 that dumps process memory whenever they crash. This DLL contains a function called MiniDumpW that is written so it can be called with rundll32.exe. The first two arguments are not used, but the third one is split into 3 parts. First part is the process ID that will be dumped, second part is the dump file location, and third part is the word full. There is no other choice.</mark>

Once these 3 arguments has been parsed, basically this DLL creates the dump file, and dumps the specified process into that dump file. Thanks to this function, we can use comsvcs.dll to dump lsass process instead of uploading procdump and executing it. (This information was extracted from [https://en.hackndo.com/remote-lsass-dump-passwords/](https://en.hackndo.com/remote-lsass-dump-passwords/))

```
rundll32.exe C:\Windows\System32\comsvcs.dll MiniDump <lsass pid> lsass.dmp full
```

{% hint style="info" %}
We just have to keep in mind that this technique can only be executed as SYSTEM
{% endhint %}
