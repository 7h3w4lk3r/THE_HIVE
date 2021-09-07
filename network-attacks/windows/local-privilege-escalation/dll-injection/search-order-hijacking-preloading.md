# Search Order Hijacking \( Preloading \)

Abusing the DLL Search Order and taking advantage of this mechanism in order for an application to load a rogue DLL instead of the legitimate one is known as DLL preloading. It is called preloading because the attackers can place their DLL earlier in the search order and thus the application loads this instead of the legitimate one. This technique is documented as DLL Search Order Hijacking in the MITRE ATT&CK framework \(T1038\). In the following sections of this article we demonstrate how legitimate applications can be abused to load and execute a Cobalt Strike beacon payload via search order hijacking.

#### Since Windows XP, when the SafeDllSearchMode option is enabled a module is loaded in the address space of an application - either during load-time or run-time - and the full path to the module is not explicitly specified or the manifest file does not state where the dependency can be looked up, the operating system searches for the DLL in a defined search order, that consists of the following:

1. The directory from which the application is loaded
2. The system directory
3. The 16-bit system directory
4. The Windows directory
5. The current directory \(same as 1 unless otherwise specified\)
6. The directories that are listed in the PATH environment variable

#### 

























