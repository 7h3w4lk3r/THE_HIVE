# Defense Evasion

### Detection Methods in AV <a id="detection-methods-in-av"></a>

In the past AVs were not as smart as they are today. AVs would almost totally rely on signature based detection to determine if the content is malicious or not. AVs would only start their action as soon as some file is written on the disk or a new process is created \(**note**: there are many more ways they would use to detect malware but these two were the most common ways to trigger AVs to start scanning\). Now AVs are more smarter and the current detection methods include \(This is not a comprehensive list but mostly seen\):-

* **Signature Based Detection:** It works by matching patterns/strings/signatures/hashes of those of a known malware from the database.
* **Heuristic Based Detection:** Similar to signature scanning, which detects threats by searching for specific strings, heuristic based detection looks for commands or instructions that would not be typically found in an application and has malicious intent.
* **Behavioral Based Detection:** This one might sound like the heuristic based one but it’s not. In this the Antivirus program looks for the events created by the program, for example if a program is trying to change or modify critical file/folder, if a program like word is spawning cmd.exe etc or if a program is calling a sequence of functions \(OpenProcess, VirtualAllocEx, WriteProcessMemory, CreateRemoteThread\) which might indicate potential process injection vector etc.
* **Sandbox Detection:** In this type of detection, the program is run in a sandbox\(virualized environment\) and it’s all behavior is recorded which is at the end analyzed automatically through a weight system in the sandbox and/or manually by a malware analyst. In this type of detection, the antivirus program will be able see in detail exactly what that file will do in that particular environment.

Be it any detection method, it’s easier for any AV products to do it while the binary is on Disk. At-least it used to be the case before AMSI, it was hard for AV products to detect fileless malware\(which doesn’t drop it’s artifacts on the disk and completely executes in the memory\). Even as of today it’s the objective of most Adverseries and Red Teamers to not touch the disk or try to reduce it as much as possible cause it just reduces the likelihood of getting detected.

###  <a id="invoke-expression"></a>

