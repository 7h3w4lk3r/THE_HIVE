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

Windows 8 introduced capability SIDs. Windows 10 has several hundred of them. Capability SIDs are used to grant applications access to resources such as the camera, or the location \([documentation](https://docs.microsoft.com/en-us/troubleshoot/windows-server/windows-security/sids-not-resolve-into-friendly-names)\).

Capability SIDs cannot be resolved to/from names, they are displayed as SID strings in permission listings. Windows ACL Editor cannot add capability SIDs, it can only delete them. To add them back use SetACL, specifying the SID string as trustee name

## Dissecting Security Descriptors \(SD\)

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

ACEs come in three flavors:

* Access **allowed** ACE
* Access **denied** ACE
* System **audit** ACE

All three variants are similar and contain the following information:

* SID of a **trustee** to whom the ACE applies
* **Access mask:** the permissions to grant/deny/audit
* **Inheritance flags:** how to propagate the ACE’s settings down the tree

Each ACE constitutes a “rule” that defines how the system is supposed to react when an attempt is made to access the object. Each rule \(ACE\) applies to exactly one trustee. The type of access that is covered by the rule is specified in the access mask.

It is important to note that a trustee for whom no rule exists has no access whatsoever to an object.

Depending on the type of the ACE the bits stored in the access mask have a different meaning.

An access allowed ACE might grant the permission to read a file. An access denied ACE would explicitly deny that kind of access. In case of a conflict \(both types of ACEs present on an object for a trustee\), the access denied ACE always has precedence!

Access allowed and denied ACEs are used in DACLs, whereas in SACLs only system audit ACEs may be used. The access mask of a system audit ACE defines the access types to be logged. If the mask were “full control”, then all kinds of access \(read, write, …\) would be audited.











