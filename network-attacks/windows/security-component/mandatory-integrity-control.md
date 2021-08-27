# Mandatory Integrity Control

Windows Vista introduced a new security construct called **Mandatory Integrity Controls \(MIC\)**, which is similar to integrity functionality long available in the Linux and Unix worlds. In Windows Vista and later versions like **Windows 7** and **Windows 10/8**, all security principals \(users, computers, services, and so on\) and objects \(files, registry keys, folders, and resources\) are given MIC labels.

## Mandatory Integrity Control

Mandatory Integrity Control \(MIC\) provides a mechanism for controlling access to securable objects and helps defend your system safe from a malicious Web, provided your browser supports them.

The purpose behind integrity controls, of course, is to give Windows another layer of defense against malicious hackers. For example, if a buffer overflow is able to crash Internet Explorer \(and not a third-party add-on or toolbar\), the resulting malicious process will often end up with Low integrity and be unable to modify Windows system files. This is the primary reason so many Internet Explorer exploits have resulted in an “important” severity rating for Windows, but a higher “critical” rating for Windows XP

Internet Explorer Protected Mode \(IEPM\) is built around mandatory integrity control. The IEPM process and extensions run at low integrity and therefore have write access only to the Temporary Internet Files\Low folder, History, Cookies, Favorites, and the `HKEY_CURRENT_USER\Software\LowRegistry` key.

While it’s completely invisible, mandatory integrity control is an important advance in maintaining the security and stability of Windows OS.

## Integrity Labels

Integrity labels specify the integrity levels of securable objects and security principals. Integrity labels are represented by [_integrity SIDs_](https://docs.microsoft.com/en-us/windows/desktop/SecGloss/i-gly). The integrity SID for a securable object is stored in its [_system access control list_](https://docs.microsoft.com/en-us/windows/desktop/SecGloss/s-gly) \(SACL\). The SACL contains a [**SYSTEM\_MANDATORY\_LABEL\_ACE**](https://docs.microsoft.com/en-us/windows/desktop/api/Winnt/ns-winnt-system_mandatory_label_ace) [_access control entry_](https://docs.microsoft.com/en-us/windows/desktop/SecGloss/a-gly) \(ACE\) that in turn contains the integrity SID. Any object without an integrity SID is treated as if it had medium integrity.

The integrity SID for a security principal is stored in its access token. An access token may contain one or more integrity SIDs.

there are several integrity lables:



* **Untrusted** – processes that are logged on anonymously are automatically designated as Untrusted. _Example: Chrome_

\_\_

* **Low** – The Low integrity level is the level used by default for interaction with the Internet. As long as Internet Explorer is run in its default state, Protected Mode, all files and processes associated with it are assigned the Low integrity level. Some folders, such as the **Temporary Internet Folder**, are also assigned the **Low integrity** level by default. However, note that a **low integrity process** is very **restricted**, it **cannot** write to the **registry** and it’s limited from writing to **most locations** in the current user’s profile. _Example: Internet Explorer or Microsoft Edge_

\_\_

* **Medium** – Medium is the context that **most objects will run in**. Standard users receive the Medium integrity level, and any object not explicitly designated with a lower or higher integrity level is Medium by default. Not that a user inside the Administrators group by default will use medium integrity levels.



* **High** – **Administrators** are granted the High integrity level. This ensures that Administrators are capable of interacting with and modifying objects assigned Medium or Low integrity levels, but can also act on other objects with a High integrity level, which standard users can not do. _Example: "Run as Administrator"_

\_\_

* **System** – As the name implies, the System integrity level is reserved for the system. The Windows kernel and core services are granted the System integrity level. Being even higher than the High integrity level of Administrators protects these core functions from being affected or compromised even by Administrators. Example: Services



* **Installer** – The Installer integrity level is a special case and is the highest of all integrity levels. By virtue of being equal to or higher than all other WIC integrity levels, objects assigned the Installer integrity level are also able to uninstall all other objects.

You can get the integrity level of a process using **Process Explorer** from **Sysinternals**, accessing the **properties** of the process and viewing the "**Security**" tab



