# Search Order Hijacking ( Preloading )

#### Abusing the DLL Search Order and taking advantage of this mechanism in order for an application to load a rogue DLL instead of the legitimate one is known as DLL preloading.

#### &#x20;It is called preloading because the attackers can place their DLL earlier in the search order and thus the application loads this instead of the legitimate one.

## <mark style="color:red;">Finding the target</mark>

#### Since Windows XP, when the SafeDllSearchMode option is enabled a module is loaded in the address space of an application - either during load-time or run-time - and the full path to the module is not explicitly specified or the manifest file does not state where the dependency can be looked up, the operating system searches for the DLL in a defined search order, that consists of the following:

1. The directory from which the application is loaded
2. The system directory
3. The 16-bit system directory
4. The Windows directory
5. The current directory (same as 1 unless otherwise specified)
6. The directories that are listed in the PATH environment variable

When the operating system starts the search for a DLL that the application is importing, unless a full path is specified the directory from which the application was loaded is searched first. If the DLL does not exist there, the system directory is searched next and so on. The abuse occurs when the attackers, having observed the behaviour of an application, place their own DLL earlier in the search order so the application loads that DLL instead of continuing the search.

## <mark style="color:red;">Exploitation</mark>

In this example the target is the google crash handler binary which is signed and trusted. in order to identify the modules that the executable attempts to load and could potentially be hijacked, we use SysInternal's Procmon (same as before) with the following filters:

![](<../../../../.gitbook/assets/image (165).png>)

After applying the filters, we get the following candidate DLLs:

![](<../../../../.gitbook/assets/image (160).png>)

Exploitation

In order to weaponize the GoogleCrashHandler.exe, we created a custom DLL that executes the reverse shell. We named the output DLL wkscli.dll, placed it in the same directory the GoogleCrashHandler executable image exists. We then opened GoogleCrashHandler. The next picture shows the rogue DLL was mapped in the address space of GoogleCrashHandler and a beacon was launched.

![](<../../../../.gitbook/assets/image (163).png>)

And if we check the listener we have a reverse shell from target system.
