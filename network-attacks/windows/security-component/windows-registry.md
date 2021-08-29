# Windows Registry

The registry is a hierarchical database that contains data that is critical for the operation of Windows and the applications and services that run on Windows. The data is structured in a tree format. Each node in the tree is called a _key_. Each key can contain both _subkeys_ and data entries called _values_. Sometimes, the presence of a key is all the data that an application requires; other times, an application opens a key and uses the values associated with the key. A key can have any number of values, and the values can be in any form. For more information, see [Registry Value Types](https://docs.microsoft.com/en-us/windows/win32/sysinfo/registry-value-types) and [Registry Element Size Limits](https://docs.microsoft.com/en-us/windows/win32/sysinfo/registry-element-size-limits).

Each key has a name consisting of one or more printable characters. Key names are not case sensitive. Key names cannot include the backslash character \(\\), but any other printable character can be used. Value names and data can include the backslash character.

The name of each subkey is unique with respect to the key that is immediately above it in the hierarchy. Key names are not localized into other languages, although values may be.

{% hint style="info" %}
It's not necessary for all Windows applications to use the Windows Registry. Some programs store their configurations in XML or other types of files instead of the registry, and others are entirely portable and store their data in an executable file.
{% endhint %}

The following illustration is an example registry key structure as displayed by the Registry Editor.

![](../../../.gitbook/assets/image%20%2868%29.png)

Each of the trees under **My Computer** is a key. The **HKEY\_LOCAL\_MACHINE** key has the following subkeys: **HARDWARE**, **SAM**, **SECURITY**, **SOFTWARE**, and **SYSTEM**. Each of these keys in turn has subkeys. For example, the **HARDWARE** key has the subkeys **DESCRIPTION**, **DEVICEMAP**, and **RESOURCEMAP**; the **DEVICEMAP** key has several subkeys including **VIDEO**

Each value consists of a value name and its associated data, if any. **MaxObjectNumber** and **VgaCompatible** are values that contain data under the **VIDEO** subkey.

#### A registry tree can be 512 levels deep. You can create up to 32 levels at a time through a single registry API call.

## Structure of the Registry

Much of the registry consists of nested containers known as subtrees, keys, and subkeys, which are like folders. The actual data is stored in the registry entries, the lowest level element in the registry. The entries are like files. The series of nested containers make up a path to each entry. Because entry names are unique only within a path, entries are referenced by their full registry path and name.



### Subtree <a id="subtree"></a>

Subtrees are the _root_, or primary divisions, of the registry. The Windows Server 2003 registry is divided into five subtrees. Subtrees themselves do not contain configuration data; they contain the keys, subkeys, and entries in which the data is stored. Click the name of a subtree to see its description:

* [HKEY\_CLASSES\_ROOT](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc739822%28v=ws.10%29)
* [HKEY\_CURRENT\_USER](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc779816%28v=ws.10%29)
* [HKEY\_LOCAL\_MACHINE](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc784983%28v=ws.10%29)
* HKEY\_USERS
* [HKEY\_CURRENT\_CONFIG](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc776168%28v=ws.10%29)



### Key <a id="key"></a>

Keys are the next level down from subtree, which contain at least one subkey \(for example, [Hardware Key](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786058%28v=ws.10%29)\). Some subtrees do not have keys.



### Subkey <a id="subkey"></a>

Subkeys are the next level down from keys or subtrees \(if the subtree has no keys\). Subkeys store entries and other subkeys.



### Entry <a id="entry"></a>

Entries are the lowest-level element in the registry. They appear in the right pane of the registry editor window. Each entry consists of the entry name, its [Data Types in the Registry](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc785172%28v=ws.10%29) \(which defines the length and format of data that the entry can store\), and a field known as the _value_ of the registry entry. Data is stored in the value. Entries are referenced by their registry path and name.

Entries store the actual configuration data for the operating system and the programs that run on the system. As such, they are different from subtrees, keys, and subkeys, which are merely containers.



### Hive Files <a id="hive-files"></a>

The permanent parts of the registry are stored as a set of files called the hive files. You can find a list of locations for these files in the hivelist subkey in HKLM\SYSTEM\CurrentControlSet\Control. These files are saved in systemroot\System32\Config and updated with each login. They consist of the following files, which store four of the five keys in HKEY\_LOCAL\_MACHINE and one key in HKEY\_USERS:

* **SAM** Contains information stored in the key HKLM\SAM about the Security Accounts Manager \(SAM\) service.
* **SECURITY** Contains the security information stored in the key HKLM\SECURITY.
* **SOFTWARE** Contains information stored in the key HKLM\SOFTWARE about the computer's software configuration.
* **SYSTEM** Contains information stored in the HKLM\SYSTEM about the computer's system configuration.
* **DEFAULT** Contains the default system information that is stored in the key HKEY\_USERS\.DEFAULT.

#### HKEY\_LOCAL\_MACHINE\HARDWARE is not stored as a file, because it is recreated each time the system starts.



### Data Types

Here is a list of common Types and what they represent:

* **REG\_BINARY** – The value will be in Binary format. Usually used for hardware component entries.
* **REG\_DWORD** – These values are commonly a “0” for a disabled option or a “1” for an enabled option.
* **REG\_SZ** – These values are stored in a human readable format.

## Registry Key Security and Access Rights <a id="registry-key-security-and-access-rights"></a>

The valid access rights for registry keys include the DELETE, READ\_CONTROL, WRITE\_DAC, and WRITE\_OWNER [standard access rights](https://docs.microsoft.com/en-us/windows/desktop/SecAuthZ/standard-access-rights). Registry keys do not support the SYNCHRONIZE standard access right.

The following table lists the specific access rights for registry key objects.



| Value | Meaning |
| :--- | :--- |
| KEY\_ALL\_ACCESS \(0xF003F\)  | Combines the STANDARD\_RIGHTS\_REQUIRED, KEY\_QUERY\_VALUE, KEY\_SET\_VALUE, KEY\_CREATE\_SUB\_KEY, KEY\_ENUMERATE\_SUB\_KEYS, KEY\_NOTIFY, and KEY\_CREATE\_LINK access rights.  |
| KEY\_CREATE\_LINK \(0x0020\)  | Reserved for system use.  |
| KEY\_CREATE\_SUB\_KEY \(0x0004\)  | Required to create a subkey of a registry key.  |
| KEY\_ENUMERATE\_SUB\_KEYS \(0x0008\)  | Required to enumerate the subkeys of a registry key.  |
| KEY\_EXECUTE \(0x20019\)  | Equivalent to KEY\_READ.  |
| KEY\_NOTIFY \(0x0010\)  | Required to request change notifications for a registry key or for subkeys of a registry key.  |
| KEY\_QUERY\_VALUE \(0x0001\)  | Required to query the values of a registry key.  |
| KEY\_READ \(0x20019\)  | Combines the STANDARD\_RIGHTS\_READ, KEY\_QUERY\_VALUE, KEY\_ENUMERATE\_SUB\_KEYS, and KEY\_NOTIFY values.  |
| KEY\_SET\_VALUE \(0x0002\)  | Required to create, delete, or set a registry value.  |
| KEY\_WOW64\_32KEY \(0x0200\)  | Indicates that an application on 64-bit Windows should operate on the 32-bit registry view. This flag is ignored by 32-bit Windows. For more information, see [Accessing an Alternate Registry View](https://docs.microsoft.com/en-us/windows/desktop/WinProg64/accessing-an-alternate-registry-view).  This flag must be combined using the OR operator with the other flags in this table that either query or access registry values.  **Windows 2000:** This flag is not supported.  |
| KEY\_WOW64\_64KEY \(0x0100\)  | Indicates that an application on 64-bit Windows should operate on the 64-bit registry view. This flag is ignored by 32-bit Windows. For more information, see [Accessing an Alternate Registry View](https://docs.microsoft.com/en-us/windows/desktop/WinProg64/accessing-an-alternate-registry-view).  This flag must be combined using the OR operator with the other flags in this table that either query or access registry values.  **Windows 2000:** This flag is not supported.  |
| KEY\_WRITE \(0x20006\)  | Combines the STANDARD\_RIGHTS\_WRITE, KEY\_SET\_VALUE, and KEY\_CREATE\_SUB\_KEY access rights. |

## Editing Registry Entries

To run reg.exe, you first need to start Command Prompt as an administrator with the following steps:

1. Open **Start**.
2. Search for **Command Prompt**.
3. Right-click the result and select **Run as administrator**.
4. To run the tool, type the following command and press **Enter**:

```text
reg /?
```

This command will display all the operation types you can use, including:

```text
reg add
reg compare
reg copy
reg delete
reg export
reg import
reg load
reg query
reg restore
reg save
reg unload
```

#### You can also use REG followed by the operation type and /? to get more help. For example, **REG QUERY /?** or **REG ADD /?**.

{% hint style="info" %}
You'll get the return codes: **0** meaning that the operation completed successfully, and **1** indicating that the operation failed. However, you won't get any return codes using the **Compare** switch.
{% endhint %}

### view registry entries

If you’re only interested in looking up a key, you will not need the name of a value. When you look up a key, you basically look up what values and keys exist under it.

```text
Reg Query "Path to key"
```

example:

```text
Reg Query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion"
```

The output of this command can be rather long since a key can have lots of sub-keys. This command will only return a list of keys under the one you’ve specified.

If you’d like to look up both the keys and the values under a specific key, and also include all the values under the sub-keys, add the /s switch. Be prepared for an even larger output.

```text
Reg Query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /s
```

Look up value

```text
Reg Query "Path to key" /v NameOfValue
Reg Query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v InstallDate
```



### add and delete registry entries

```text
Add: REG ADD KeyName [{/v ValueName | /ve}] [/t Type] [/f]
Delete: REG DELETE KeyName [{/v ValueName | /ve | /va}] [/f]
```

**KeyName:** Defines the path to the subkey or entry. Valid registry key shortcuts include HKLM, HKCU, HKCR, HKU, and HKCC. If you're trying to edit the registry on a remote computer, you can only use these shortcuts: HKLM and HKU.

**/v ValueName:** Specifies the name for the registry key to be added or deleted.

**/ve:** Defines if you're adding or deleting an entry that has a null value.

**/t Type:** Specifies the type of registry entries. Here's the list of valid types:

* REG\_SZ
* REG\_MULTI\_SZ
* REG\_DWORD\_BIG\_ENDIAN
* REG\_DWORD
* REG\_BINARY
* REG\_DWORD\_LITTLE\_ENDIAN
* REG\_LINK
* REG\_FULL\_RESOURCE\_DESCRIPTOR
* REG\_EXPAND\_SZ

**/f:** Adds or deletes registry content without prompting for confirmation.

**/s Separator:** Defines the character you use to separate multiple instances of data when the **REG\_MULTI\_SZ** data type is specified and you need to add more than one entry. The default separator is **\0** if it is not specified.

**/d Data:** Specifies the data for the new entry in the registry.

example:

To add a subkey named **MySubkey** under **HKEY\_LOCAL\_MACHINE\Software**, use the following example:

```text
REG ADD HKLM\Software\MySubkey
```

To add a new DWORD \(32-bit\) value entry named **AppInfo** with the value of **1**, use the following example:

```text
REG ADD HKLM\Software\MySubkey /v AppInfo /t REG_DWORD /d 1
```

To add a new DWORD \(32-bit\) value entry named **AppInfo** with value of **1** on a remote computer, use the following example:

```text
REG ADD \\ComputerName\HKLM\Software\MySubkey /v AppInfo /t REG_DWORD /d 1
```

To add a new Binary Value entry named **Data** with data of **fe340ead**, use the following example:

```text
REG ADD HKLM\Software\MySubkey /v Data /t REG_BINARY /d fe340ead
```

To add a registry entry with multiple values to **MySubkey** with a value name of MRU of type **REG\_MULTI\_SZ** and data of **fax\0mail\2\1**, use the following example:

```text
REG ADD HKLM\Software\MySubkey /v MRU /t REG_MULTI_SZ /d fax\0mail\2\1
```

To add an expanded registry entry to **MySubkey** with a value name of **Path** of type **REG\_EXPAND\_SZ** and data of **%systemroot%**, use the following example:

```text
REG ADD HKLM\Software\MySubkey /v Path /t REG_EXPAND_SZ /d ^%systemroot^%
```

To delete the subkey named **MySubkey**, use the following example:

```text
REG DELETE HKLM\Software\MySubkey /f
```

### 

### copy registry entries

```text
Copy: REG COPY KeyName1 KeyName2 [/s] [/f]
```

* **KeyName1:** Defines the path to the subkey you want to copy. Valid registry key shortcuts include HKLM, HKCU, HKCR, HKU, and HKCC. If you're trying to copy the registry on a remote computer, you can only use these shortcuts: HKLM and HKU.
* **KeyName2:** Defines the path to the subkey destination. Valid registry key shortcuts include HKLM, HKCU, HKCR, HKU, and HKCC. If you're trying to copy the registry on a remote computer, you can only use these shortcuts: HKLM and HKU.
* **/s:** Copies all subkeys and entries of a particular subkey.
* **/f:** Executes the copy command without prompting for confirmation.

example:

```text
REG COPY HKLM\Software\MySubkey1 HKLM\Software\MySubkey2 /s
```

To copy all values under the subkey **MySubkey1** from a remote computer to the subkey **MySubkey2** on the a new computer, use the following example:

```text
REG COPY \\ComputerName\HKLM\Software\MySubkey1 HKLM\Software\MySubkey2
```

### 

### export and import registry entries

```text
Export: REG EXPORT KeyName FileName [/y]
Import: REG IMPORT FileName
```

* **KeyName:** Defines the path to the subkey or entry. Valid registry key shortcuts include HKLM, HKCU, HKCR, HKU, and HKCC.
* **FileName:** Specifies the name and path of the .reg file to be exported or imported.
* **/y:** Overwrites the registry content without prompting for confirmation.

To export all the content within the subkey **MySubkey**, use the following example:

```text
REG EXPORT HKLM\Software\MySubkey C:\RegKeyBackup.reg
```

To export and overwrite any existing file, use the following example:

```text
REG EXPORT HKLM\Software\MySubkey C:\RegKeyBackup.reg /y
```

To import all the content, including subkeys, entries, and values within the subkey named **MySubkey**, use the following example:

```text
REG IMPORT C:\RegKeyBackup.reg
```

### 

### save and restore registry entries

```text
Save: REG SAVE KeyName FileName [/y]
Restore: REG RESTORE KeyName FileName
```

* **KeyName:** Defines the path to the subkey or entry. Valid registry key shortcuts include HKLM, HKCU, HKCR, HKU, and HKCC. If you're trying to edit the registry on a remote computer, you can only use these shortcuts: HKLM and HKU.
* **FileName:** Specifies the name and path of the .hiv file to be saved or restored.
* **/y:** Overwrites the registry content without prompting for confirmation.

To save a copy of subkeys, entries, and values within the subkey named **MySubkey**, use the following example:

```text
REG SAVE HKLM\Software\MySubkey C:\RegKeyBackup.hiv /y
```

To restore all the content, including subkeys, entries, and values within the subkey named **MySubkey**, use the following example:

```text
REG RESTORE HKLM\Software\MySubkey C:\RegKeyBackup.hiv
```







