# DLL Side-Loading

DLL Side-Loading Programs may specify DLLs that are loaded at runtime. Programs that improperly or vaguely specify a required DLL may be open to a vulnerability in which an unintended DLL is loaded. Side-loading vulnerabilities specifically occur when Windows Side-by-Side \(WinSxS\) manifests are not explicit enough about characteristics of the DLL to be loaded. Adversaries may take advantage of a legitimate program that is vulnerable to side-loading to load a malicious DLL.

Adversaries likely use this technique as a means of masking actions they perform under a legitimate, trusted system or software process.

![](../../../../../.gitbook/assets/image%20%28159%29.png)

WinSxS feature is used by many applications to prevent problems that can arise due to updated and duplicated version of DLLs. DLL side-loading attack makes use of WinSxS directory. This is located in C:WindowsWinSxS and holds multiple versions of DLLs. When an application uses this directory to retrieve DLL, then it needs to have a manifest. Manifest lists the DLL that can be used at run time by this application and is used by loaded to determine which version of DLL to use.

Now this technique/folder has less validation other than what is specified in the metadata itself and thus a spoofed DLL can be placed in this directory and loaded. A well-documented attack of DLL side-loading by FireEye can be seen [here](https://www.fireeye.com/content/dam/fireeye-www/global/en/current-threats/pdfs/rpt-dll-sideloading.pdf).

You can find a detailed demo of a DLL side-loading scenario in this youtube video:

{% embed url="https://www.youtube.com/watch?v=uPl28hTfFBs" %}

