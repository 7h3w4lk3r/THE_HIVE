# ⭕ SCR

**SCR files are screensaver files used by Windows for energy saving purposes.** Screensavers are programs that execute after a configurable time of user inactivity and consists of Portable Executable(PE) files with a `.scr` file extension. The Windows screensaver application scrnsave.scr is located in `C:\Windows\System32` , and `C;\Windoww\sysWOW64\` on 64-bit Windows systems. The screensaver settings are stored in the Registry (`HKCU:\Control Panel\Desktop\`) and can also be manipulated for persistence.

SCR extensions are also executables on a Windows machine we can go a bit more farther and try to make this more credible program to execute.

#### <mark style="color:green;">simply create a PE payload and change the extension from</mark> <mark style="color:green;"></mark><mark style="color:green;">`.exe`</mark> <mark style="color:green;"></mark><mark style="color:green;">to</mark> <mark style="color:green;"></mark><mark style="color:green;">`.scr`</mark>

{% hint style="info" %}
#### This techniques is useful for persistence as well.

#### see [screensaver hijack](../windows/persistence/screensavers-hijack.md) section.
{% endhint %}
