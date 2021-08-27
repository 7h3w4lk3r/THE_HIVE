# Permissions

Every object that can have a security descriptor \(SD\) is a securable object that may be protected by permissions. All named and several unnamed Windows objects are securable and can have SDs, although this is not widely known. There does not even exist a GUI for manipulating the SDs of many object types! Have you ever tried to kill a system process in Task Manager and got the message “Access denied”? This is due to the fact that this process’ SD does not allow even administrators to kill the process. But it is, of course, possible, as an administrator, to obtain the necessary permissions, provided a GUI or some other tool is available.

## Securable Objects

#### Among many others, the following object types are securable:

* Files and directories on NTFS volumes
* Registry keys \(but not values\)
* Network shares
* Printers
* Services
* Active Directory objects
* Processes

Of these types, some are hierarchical in nature \(directories, registry keys, …\), and some are not \(printers, services, …\).

## Security Descriptor \(SD\)

#### A security descriptor is a binary data structure that contains all information related to access control for a specific object. An SD may contain the following information:

* The **owner** of the object
* The **primary group** of the object \(rarely used\)
* The discretionary access control list \(**DACL**\)
* The system access control list \(**SACL**\)
* Control information

## Security IDs \(SID\)

Any trustee can be identified by its name or by its SID. Humans tend to prefer names whereas computers very much prefer SIDs, which are binary data structures. When humans cannot avoid dealing with SIDs they use a certain string format. In this format, the SID of the local group `Administrators` looks like this: `S-1-5-32-544`.

### **SID to Name Lookup**

It is important to remember that trustees referenced in SDs are always stored as binary SIDs. This is true for the owner, the primary group, and any trustee in any access control list \(ACL\). This implies that there exists some mechanism that converts trustee names into SIDs and vice versa. This mechanism is a central part of the security accounts manager \(SAM\) and of Active Directory \(AD\). The former manages the local account database on any NT-based system \(Windows NT right up to Windows 10, including the server variants\). The latter is only available on Active Directory domain controllers where it replaces the SAM.

### **Special SID Types**

#### **Capability SIDs**

#### Windows 8 introduced capability SIDs. Windows 10 has several hundred of them. Capability SIDs are used to grant applications access to resources such as the camera, or the location \([documentation](https://docs.microsoft.com/en-us/troubleshoot/windows-server/windows-security/sids-not-resolve-into-friendly-names)\).

Capability SIDs cannot be resolved to/from names, they are displayed as SID strings in permission listings. Windows ACL Editor cannot add capability SIDs, it can only delete them. To add them back use SetACL, specifying the SID string as trustee name

## Dissecting Security Descriptors \(SD\)

### \*\*\*\*

### **Control Information**

The control information of an SD contains various bit flags, of which the two most important bits specify whether the DACL respectively SACL are protected. If an ACL is protected, it does not inherit permissions from its parent. Inheritance is discussed in more detail later.



### **Owner**

An object can, but need not have, an owner. Most objects do, though. The owner of an object has the privilege of being able to manipulate the object’s DACL regardless of any other settings in the SD. The ability to set _any_ object’s owner is controlled by the privilege \(user right, see below\) `SeTakeOwnershipPrivilege`, which typically is only held by the local group `Administrators`.

### **Primary Group**

The primary group of an object is rarely used. Most services and applications ignore this property.



### **DACL**

The DACL is controlled by the owner of the object and specifies what level of access particular trustees have to the object. It can be NULL or nonexistent \(no restrictions, everyone full access\), empty \(no access at all\), or a list, as the name implies. The DACL almost always contains one or more access control entries \(ACEs\). A more detailed description of ACLs and ACEs can be found below.



### **SACL**

The SACL specifies which attempts to access the object are audited in the security event log. The ability to get or set \(read or write\) any object’s SACL is controlled by the privilege \(user right, see below\) `SeSecurityPrivilege`, which typically is only held by the local group `Administrators`.





## Access Control Lists \(ACL\) and Access Control Entries \(ACE\)

As mentioned earlier, an ACL contains a list of access control entries \(ACEs\). The maximum number of ACEs is not limited, but the size of the ACL is: it must not be larger than 64 KB. This may not seem much, but should in practice be more than sufficient. Should you ever come in a situation where those 64 KB are not enough, I suggest you review your security concept from the very beginning.

#### ACEs come in three flavors:

* Access **allowed** ACE
* Access **denied** ACE
* System **audit** ACE

#### All three variants are similar and contain the following information:

* SID of a **trustee** to whom the ACE applies
* **Access mask:** the permissions to grant/deny/audit
* **Inheritance flags:** how to propagate the ACE’s settings down the tree

#### Each ACE constitutes a “rule” that defines how the system is supposed to react when an attempt is made to access the object. Each rule \(ACE\) applies to exactly one trustee. The type of access that is covered by the rule is specified in the access mask.

It is important to note that a trustee for whom no rule exists has no access whatsoever to an object.

Depending on the type of the ACE the bits stored in the access mask have a different meaning.

An access allowed ACE might grant the permission to read a file. An access denied ACE would explicitly deny that kind of access. In case of a conflict \(both types of ACEs present on an object for a trustee\), the access denied ACE always has precedence!

Access allowed and denied ACEs are used in DACLs, whereas in SACLs only system audit ACEs may be used. The access mask of a system audit ACE defines the access types to be logged. If the mask were “full control”, then all kinds of access \(read, write, …\) would be audited.



## Inheritance

In Windows 2000 the security model was supplemented with the concept of inheritance. Each ACE has inheritance flags that control how the ACE is to be propagated to child objects. The most common case is full inheritance: child objects inherit all ACEs from their parent and have therefore identical resulting permissions and auditing settings.

It is important to note here that an ACE that has been inherited from a parent is marked as being inherited, and cannot be modified on the child object! By means of this mark \(or flag\) the system is able to tell whether an ACE is set directly on the object or whether it has been inherited from a parent. This feature makes it possible to create permission structures on, for example, a home directory partition, like the following:



| Path | Trustee | Permissions |
| :--- | :--- | :--- |
| e:\ | administrators, system | full control |
| e:\users | help desk | change |
| e:\users\user1 | user1 | change |
| e:\users\user2 | user2 | change |

The resulting set of permissions on the folder `e:\users\user1` would be:

| Trustee | Permission |
| :--- | :--- |
| administrators | full control |
| system | full control |
| help desk | change |
| user1 | change |

Until here, all this would have been possible with NT4, too. But now we want to add another group to `e:\` and want it to have full control over the entire drive. This is not possible on NT4 without resetting the permissions on all child objects and thus losing the users’ permissions \(and the help desk’s\). In Windows 2000 the system simply adds a new ACE to every object in the directory tree below `e:\` and marks those ACEs as inherited.

### **Inheritance Flags**

It is, of course, possible to specify exactly how an ACE is to be inherited by its children. The following inheritance flags can be used individually or in any combination:

* **container inherit:** child containers \(e.g. directories\) inherit the ACE
* **object inherit:** child objects \(e.g. files\) inherit the ACE
* **inherit only:** the ACE does not apply to the object itself, but can be inherited by children
* **no propagation:** the ACE may not be inherited by children

The settings available in Windows ACL Editor \(see below\) correspond to the following combinations:

* **This folder only:** no propagation
* **This folder, subfolders and files:** container inherit + object inherit
* **This folder and subfolders:** container inherit
* **This folder and files:** object inherit
* **Subfolders and files only:** container inherit + object inherit + inherit only
* **Subfolders only:** container inherit + inherit only
* **Files only:** object inherit + inherit only

As mentioned earlier, an object can block inheritance from its parents. If this flag is set, the object is said to be “protected”. Blocking inheritance should be avoided wherever possible since a directory tree where all objects are protected essentially uses the NT4 style security model with all its disadvantages \(and there are many!\).

### Taking Ownership

A member of the local group `Administrators` can always take ownership of any object on the system. Once that is done, the administrator has full control over the object, can manipulate the DACL and SACL, and can even set the ownership back to the original trustee. The latter is not possible with the GUI \(ACL Editor\) but can be accomplished through the security API.

## Privileges and Rights

Privileges, or rights, as they are often called, are very different from permissions. A privilege allows the exertion of permissions \(the right to log on makes it possible to access those files you have permissions for\). Privileges are configured in the local security policy or a domain Group Policy object. Three privileges are noteworthy in this context:

| Privilege Name | Description |
| :--- | :--- |
| SeSecurityPrivilege | Read and write access to all SACLs |
| SeBackupPrivilege | Circumvent NTFS permissions and read \(back up\) every file and every folder |
| SeRestorePrivilege | Circumvent NTFS permissions and write \(restore\) every file and every folder |
| SeTakeOwnershipPrivilege | Set the owner of any securable object |

## Windows ACL Editor

The GUI provided by Windows to manipulate SDs is called ACL Editor. It can be accessed by right-clicking a file and choosing **Properties** &gt; **Security**. I am not going to describe ACL Editor in detail, but rather point out some of its features and peculiarities.

ACL Editor is a remarkable piece of software. It handles nearly all side effects of the transition from the NT4 to the W2k style security model. It does this so well that most administrators are not even aware of the inherent problems and difficulties. Consider this scenario: you upgraded your file server from NT4 to W2k. Then you open ACL Editor on an arbitrary directory and it tells you that there are inherited permissions from the parent folder. Which is, technically speaking, not true. During the upgrade process, the security descriptors are not changed, which means the flag which marks an ACE as having been inherited is not set, not for one single directory \(or registry key, for that matter\).

And yet, ACL Editor tells you there are inherited ACEs. On XP and W2k3 it even shows you which parent object the ACEs were inherited from. This is done by comparing the object’s ACEs to the object’s parent’s ACEs and determining which ACEs would be marked as inherited had the permissions been set using W2k instead of NT4. All this is done online when you open ACL Editor.

This brings me to an important point: ACL Editor does not necessarily show what’s there, but **displays an interpretation of an ACL**. SetACL, on the other hand, shows you exactly what is stored in an ACL – thus it is possible that both tools list different ACEs in one and the same ACL. This happens frequently in a situation similar to the following:

| Path | Trustee | Permissions | Inheritance |
| :--- | :--- | :--- | :--- |
| e:\data | Administrators | full control | no propagation |
| e:\data | Administrators | full control | container inherit + object inherit + inherit only |

Of course, both ACEs taken together combine to the standard “Full control” for the group `Administrators` on the folder `e:\data` and all of its subfolders and files. That is what ACL Editor shows you: one entry, instead of two, even in advanced view. SetACL, which does not “interpret” ACEs in any way, shows two ACEs. On my Windows XP installation, which has, of course, not been upgraded from NT, this behavior can be observed on the `Program Files` directory.



