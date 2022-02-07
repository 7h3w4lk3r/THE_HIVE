# Active Directory Components



![](<../../../.gitbook/assets/image (168).png>)

### **Forests**&#x20;

Represents a complete Active Directory instance. **It is made of one or more domains and domain trees.** Each domain has its own characteristics, boundaries, and resources. But at the same time, it shares a common logical structure, schema, and directory configuration within the forest. Similarly, tribes have a relationship with the forest and other tribes, and **domains in the Active Directory forest will have a two-way trust relationship. The first domain controller in the Active Directory service deployment is the forest root domain.**

{% hint style="info" %}
Two-way transitive trust is a logical link between domains, where the trusting domain honors the logon authentication of the trusted domain. When considering the previous example, users in rebeladminit.com can authenticate into mytraining.ca , and vice versa.

Any object located in a particular domain inherently trusts other objects in other domains in the same forest. This is not the same as when considering authentication between forests. For that, it may (depending on the trust method) require additional login credentials. An organization can have a single forest or multiple forests based on the company's business requirements.
{% endhint %}

### Trees

A domain tree is **a collection of domains that reflects the organization's structure. domains inside the domain tree have a parent-child relationship.** The first domain in the domain tree is called the parent domain. This is also the root domain. All other domains in the domain tree are called the child domains. There will be only one parent domain in a domain tree. **In some documentation, child domains are also called subdomains.** When dealing with internet domains, the creation of an additional placeholder, a sub-URL, is sometimes required

### Domain

The domain **contains the logical components to achieve the administrative goals of the organization.** By default, **the domain becomes the security boundary for the objects inside it.** Each object has its own administrative goals. **Individuals in tribes have different identities and responsibilities, but all of them are part of the tribe and the forest.** In the same way, all the objects in the domain are part of a common database. Also, everyone in the tribe still needs to follow some of the common rules. Objects in the domain are also controlled by the defined security rules.

### Organizational units

**Organizational units help group objects on a smaller scale within the domain.** The most common way is to group objects that have similar security and administrative requirements together.When deploying a domain controller, it creates a default OU structure to segment the most common object types, such as users, computers, and domain controllers. The administrator can add, remove, and delete an OU as required.

**Once an object is assigned to an OU, it inherits the security settings and permissions that are applied to the OU level**. **If the same object is moved to a different OU, then it will apply the settings from the new OU, and discard the settings that were applied from the previous OU.** OUs also help to delegate administrative control to individuals for specific tasks. Domain administrators have privileges that allow them to manage any object within the domain. But it's possible to create administrators and assign them to manage objects and resources on an OU level. **For these administrators, the OU will be the security boundary. They will not be able to modify any other object outside that particular OU**.

{% hint style="info" %}
OUs can also contain object types, such as users, groups, contacts, computers, organizational units, and printers
{% endhint %}

### Domain Trust

At a high level, a **domain trust** establishes the ability for **users in one domain to authenticate** to resources or act as a security principal **in another domain**.

Essentially, all a trust does is **linking up the authentication systems of two domains** and allowing authentication traffic to flow between them through a system of referrals. When **2 domains trust each other they exchange keys**, these **keys** are going to be **saved** in the **DCs** of **each domains** (**1 key per trust direction**) and the keys will be the base of the trust.

When a **user** tries to **access** a **service** on the **trusting domain** it will request an **inter-realm TGT** to the DC of its domain. The DC wills serve the client this **TGT** which would be **encrypted/signed** with the **inter-realm** **key** (the key both domains **exchanged**). Then, the **client** will **access** the **DC of the other domain** and will **request** a **TGS** for the service using the **inter-realm TGT**. The **DC** of the trusting domain will **check** the **key** used, if it's ok, it will **trust everything in that ticket** and will serve the TGS to the client.

![](<../../../.gitbook/assets/image (173).png>)

It's important to notice that **a trust can be 1 way or 2 ways**. In the 2 ways options, both domains will trust each other, but in the **1 way** trust relation one of the domains will be the **trusted** and the other the **trusting** domain. In the last case, **you will only be able to access resources inside the trusting domain from the trusted one**.

A trust relationship can also be **transitive** (A trust B, B trust C, then A trust C) or **non-transitive**.

**Different trusting relationships:**

* **Parent/Child** – part of the same forest – a child domain retains an implicit two-way transitive trust with its parent. This is probably the most common type of trust that you’ll encounter.
* **Cross-link** – aka a “shortcut trust” between child domains to improve referral times. Normally referrals in a complex forest have to filter up to the forest root and then back down to the target domain, so for a geographically spread out scenario, cross-links can make sense to cut down on authentication times.
* **External** – an implicitly non-transitive trust created between disparate domains. “External trusts provide access to resources in a domain outside of the forest that is not already joined by a forest trust.” External trusts enforce SID filtering, a security protection covered later in this post.
* **Tree-root** – an implicit two-way transitive trust between the forest root domain and the new tree root you’re adding. I haven’t encountered tree-root trusts too often, but from the Microsoft documentation, they’re created when you when you create a new domain tree in a forest. These are intra-forest trusts, and they preserve two-way transitivity while allowing the tree to have a separate domain name (instead of child.parent.com).
* **Forest** – a transitive trust between one forest root domain and another forest root domain. Forest trusts also enforce SID filtering.
* **MIT** – a trust with a non-Windows RFC4120-compliant Kerberos domain. I hope to dive more into MIT trusts in the future.



### Schema

**The schema is the foundation of object structures for the entire forest**. **Every domain in the forest shares the same set of object structures that are defined in the schema.** If an attacker accesses or modifies the schema, every domain in the forest will be affected. The schema is one-third of the directory database, which is stored on all domain controllers in every domain. **Only one domain controller in the forest can update the schema?the Schema Master.**

****

### Account Policy

The Account Policy for domain users is established at the domain level. The Account Policy for the domain level includes control over passwords, account lockout, and Kerberos authentication. This means that domain user accounts cannot be controlled at the organizational unit level; they must be controlled at the domain level. Also, the Account Policy is not inherited from the parent domain, if we are focusing in on a child domain. There is no possible way to get a parent domain to push down Account Policies to child domains.\


### Group Policy Objects

GPOs are the main form of pushing out security to computers in the domain. However, GPOs that are configured within a domain do not and cannot span multiple domains by inheritance or hierarchy. The GPOs can be available to other domains, but there is no option to configure GPOs to span domains with a single configuration.

****

### Delegation

By far one of the most important features of Active Directory is delegation. However, delegation without a solid OU design is almost impossible to implement. OUs need to be designed to delegate administration. The key to delegation is to have the OU contain the objects that the delegate will control. For example, if you have delegated the ability for the HR manager to reset passwords for only the HR employees, then there needs to be an OU for these user accounts. A good design would have an OU named HR\_employees, which contains only the user accounts of the HR employees. The design would have this OU low in the OU hierarchy, so that no other OUs are below it. In that design, the HR manager will not have control over any other user accounts by default.\


### GPO deployment

Many administrators leave out the consideration of GPO deployment when they design OUs. This is a mistake, mainly for security reasons. The GPO deployment should be interwoven with delegation considerations. If there is a conflict between the two design needs, the delegation needs usually win. In this case, the GPO deployment will be taken care of by filtering the GPO (setting permissions on the GPO). An example of a typical GPO design would be the configuration of the Internet Explorer proxy settings for a branch office. All employees in the branch office need to have the same proxy settings for IE, which can easily be set by using GPOs. In this case, there would be an OU named Branch1\_employees, which contains the user accounts for only the branch office. This OU would be low in the OU hierarchy, with no other OUs below it.

An error that many companies make is to duplicate their company's organizational chart for their OU design. The OUs are not well suited for this model, since this model usually breaks how the administration of objects and deployment of GPOs are implemented. This is not to say that a small percentage of companies have not successfully used the org chart for the OU design, but in most cases it will cause more anguish than benefit.



{% hint style="info" %}
This section is an overview of different Active Directory components taken from the book: <mark style="color:orange;">Mastering Active Directory</mark> by <mark style="color:orange;">Dishan Francis.</mark>
{% endhint %}

## <mark style="color:red;">Domain controllers</mark>

The domain controller is a computer that runs a Windows server operating system, and holds the Active Directory Domain Services role. It can be either a physical server or a virtual server.

The domain controller holds the directory partition that will be replicated to the other domain controllers in the same domain. The domain can have any number of domain controllers

In Windows NT, it uses multiple domain controllers, but it maintains a single-master schema. This means that directory changes can only be made from a specific domain controller.

Any object-level changes made in one domain controller will be replicated to all other domain controllers (directory service-related). That said, some of the Active Directory-related operational role changes can only be modified by the designated operation master role owner (FSMO roles).

## <mark style="color:red;">Read-only domain controllers</mark>

RODC <mark style="color:red;"></mark> allows organizations to have domain controllers in locations where data security and network security cannot be guaranteed.&#x20;

Domain controllers contain a writable copy of the AD DS database. It is replicated among all the domain controllers in the same domain, but the read-only domain controller will have a read-only AD DS database.

{% hint style="info" %}
This feature is useful in a branch network
{% endhint %}

The RODC holds a copy of Active Directory objects and attributes from writable domain controllers, except the account passwords. If any changes need to be made to objects, they need to be made in a writable domain controller. Sometimes, the branch office may host applications that need write capabilities to the directory services. These requests will be pointed to the writable domain controller instead of the RODC.

## <mark style="color:red;">Global catalog server</mark>

The global catalog server holds the full writable copy of objects in its host domain, and the partial copy of the objects in other domains in the same forest. The partial replica contains a copy of every object in the forest and the most commonly used attributes in queries.&#x20;

Applications and users in one domain can query for the objects in another domain (in the same forest) via the global catalog server.&#x20;

All domain controllers in the domain will not be global catalog servers by default. When installing the first domain controller, it will become the global catalog server, and other domain controllers can be promoted as global catalog servers according to the business requirements. Not every domain controller in the domain needs to be a global catalog server.

## <mark style="color:red;">Active Directory sites</mark>

The Active Directory site defines a physical topology of the network. Sites can be separate buildings in a campus network, with the branch office in a separate city or even in a separate country.

## <mark style="color:red;">Active Directory objects</mark>

In Active Directory, there are two types of objects:

1. <mark style="color:orange;">Container objects</mark> can store other objects in the Active Directory. The domain itself is an example of a container object. The organizational unit is also a container object.
2. <mark style="color:orange;">Leaf objects</mark> cannot store other objects in Active Directory. A service account is an example of a leaf object.

#### Active Directory objects use attributes to describe their nature.

## <mark style="color:red;">Globally unique identifiers and security identifiers</mark>

Every time we create an object in Active Directory, it will be assigned with one or two unique values.&#x20;

If it is a user or group object, it will receive a **globally unique identifier (GUID)** and a **security identifier (SID)**. The GUID value will be saved in the `objectGUID` attribute in each object and the SID value will be saved in the `objectSid` attribute in each object.

In order to view the GUID and SID values for the user account, the following PowerShell command can be run from the domain controller:

```powershell
Get-ADUser username
```

![](<../../../.gitbook/assets/image (281) (1) (1).png>)

`ObjectGUID` is a 128-bit value, and is applied to each and every object in Active Directory. This value is not just for the particular Active Directory domain. It is valid globally as well. Once a GUID is assigned to an object, it will be there until the object is deleted from the directory. Modifying or moving objects will not change the value of the GUID. The ObjectGUID attribute value will be published to the global catalog servers. If an application in a domain needs to search for a user object, the best method will be to query using ObjectGUID , as it will give an accurate result.

The SID value for an object is unique within its domain. The SID values associated with the user will be changed if the user object is migrated to another domain. An SID value assigned by one domain will not be accepted by another domain. As soon as a user object is migrated to another domain, a new SID value will be generated. Then, the old SID value will be saved in the sIDHistory attribute. This attribute can contain multiple values. When the system creates a Kerberos ticket for user authentication, it will consider a new SID value and all other SID values listed in the sIDHistory attribute.

This ACL uses the SID values. So, if an object moves to a different domain without sIDHistory , it will lose its access to resources until the ACL is modified. But if the system considers sIDHistory when granting an access token, and if the old SID value is moved over to the new domain, the user is still allowed to access the resources they were assigned.

## <mark style="color:red;">Distinguished names</mark>

Can also be used to uniquely identify an object.

There are three types of Active Directory naming attributes that have been used to generate distinguishing names:

1. <mark style="color:orange;">organizationName (O)</mark> or <mark style="color:orange;">organizationalUnitName (OU):</mark> Organization represents the root-level domain. The OU is where the object is located.
2. <mark style="color:orange;">domainComponent (DC):</mark> This is the naming attribute for the domain and the DNS. If the DNS name for the domain is rebeladmin.com , the domain components for it will be DC=rebeladmin,DC=com.
3. <mark style="color:orange;">commonName (CN):</mark> This refers to the objects and containers within the directory.

example:

```
CN=Dishan Francis,CN=Users,DC=rebeladmin,DC=com
```

Here, DC=rebeladmin,DC=com represents the domain name, CN=Users represents the user container, and at the end, CN=Dishan Francis represents the actual object name.

&#x20;<mark style="color:orange;">Relative distinguished name (RDN)</mark> is a unique value within its parent container. For the preceding example, the RDN for the object is CN=Dishan Francis . Active Directory allows you to have the same RDN for multiple objects within the directory, but all of them need to be in separate containers. It is not permitted to have the same RDN for the object within the same container.

Changing values in the object will not modify the SID value. But if the hierarchical path was changed for an object, the Distinguished Name (DN) will be changed. For example, if you move a user object from one OU to another, the DN value for the user object will be changed.

## <mark style="color:red;">Active Directory server roles</mark>

There are five main Active Directory server roles:

1. Active Directory Domain Services (AD DS)
2. Active Directory Federation Services (AD FS)
3. Active Directory Lightweight Directory Services (AD LDS)
4. Active Directory Rights Management Services (AD RMS)
5. Active Directory Certificate Services (AD CS)

Each of these server roles can be installed and configured using PowerShell. The following PowerShell cmdlets can be used to install Active Directory server roles:

| PowerShell cmdlets                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Install- WindowsFeature AD-Domain-Services | AD DS                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Install- WindowsFeature AD FS- Federation  |  AD FS                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| Install-WindowsFeature ADLDS               | AD LDS                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| Install- WindowsFeature ADRMS              | AD RMS - This role has two subfeatures, which are AD Rights Management Server and Identity Federation Support. If required, these individual roles can be installed using `Install- WindowsFeature ADRMS, ADRMS-Server, ADRMS-Identity` or `Install- WindowsFeature ADRMS -IncludeAllSubFeature` . It will install all the subfeatures.                                                                                                                                      |
| Install- WindowsFeature AD-Certificate     | AD CS - This role has six subroles, which are certification authority ( `ADCS-Cert- Authority` ), Certificate Enrollment Policy Web Service ( `ADCS-Enroll-Web-Po`l ), Certificate Enrollment Web Service ( `ADCS-Enroll-Web-Svc` ), Certification Authority Web Enrollment ( `ADCS-Web-Enrollment` ), Network Device Enrollment Service ( `ADCS-Device-Enrollment` ), and Online Responder ( `ADCS-Online-Cert` ). These subfeatures can be added individually or together. |

## <mark style="color:red;">Federation Services</mark>

AD FS allows you to share identities between trusted identity infrastructures and is based on a claim-based authorization (CBA) mechanism.

It will trust identities from completely different identity infrastructures, and pass identity information as claims to the organization that hosts the applications. Then, the company that hosts the application will map these claims to claims that the application understands, and make the authorization decisions.&#x20;

In normal scenarios, if you share a web-based system or application between two identity infrastructures, the partner organizations need to provide two credentials. One credential is to authenticate themselves with their own infrastructure, and the second one is to authenticate themselves with the remote infrastructure. AD FS allows users to have a SSO experience with the application.

{% hint style="info" %}
AD FS can be hosted in a demilitarized zone (DMZ) in the network, and it will be the only public- facing interface for the applications.
{% endhint %}

#### There are four AD FS role services:

1. <mark style="color:orange;">Federation service:</mark> The federation servers' hosted federation service will route authentication requests from identities in another identity infrastructure using a federated web SSO method, or from clients through the internet using the web SSO design method.
2. F<mark style="color:orange;">ederation Service Proxy:</mark> Federation proxy servers can be places in the DMZ (the perimeter network segment) and can forward claims to the federation service located in a secure network.
3. <mark style="color:orange;">Claims-aware agent:</mark> AD FS uses claims to create trust between two identity infrastructures. The claims-aware agent can be used in the application web server to allow queries for AD FS claims. Then, theapplication will use claims in the AD FS security token to make the authorization decision.
4. <mark style="color:orange;">Windows Token-based agent:</mark> This agent is to be installed on a web server that hosts a Windows Token-based application. It will convert the AD FS security token into a Windows access token, and the application will make an authorization decision based on this.

#### These federation roles can be installed on separate servers based on the organization's federation requirements.

## <mark style="color:red;">Lightweight Directory Services</mark>

AD LDS allows you to maintain an independent schema with each AD LDS instance. You can also host multiple AD LDS instances on one computer.

{% hint style="info" %}
AD DS and AD LDS are both builds based on the same core directory service technologies. AD LDS does not need to depend on the Active Directory domain or forest setup. But in an AD DS environment, AD LDS can use AD DS for authentication.
{% endhint %}

## <mark style="color:red;">Rights Management Services</mark>

AD RMS help organizations to protect sensitive data from unauthorized access.

AD RMS contain two roles services:

1. <mark style="color:orange;">Active Directory Rights Management Server:</mark> This installs the AD RMS server service that requires you to protect the content in an organization.
2. <mark style="color:orange;">Identity Federation Support:</mark> The AD RMS service also supports integration with AD FS services. It will allow you to protect content shared between two organizations, without setting up AD RMS in both infrastructures. This role service helps integrate AD RMS with AD FS.

## <mark style="color:red;">Certificate Services</mark>

AD CS helps organizations build public key infrastructure (PKI) in an easy, cost-effective way. Digital certificates issued by the certification authority can be used to authenticate users, computers, and devices. The certification authority is responsible for receiving certificate requests, verifying certificate requests, and issuing, renewing, and revoking certificates.

There are six role services for AD CS:

1. <mark style="color:orange;">Certification authority (CA):</mark> Mainly, there are two types of CA. Microsoft named them root and subordinate CA. The placement of these on a network will be dependent on the PKI design. CA is responsible for issuing certificates to users, computers, and devices. It will also manage the validity of certificates.
2. <mark style="color:orange;">Certification Authority Web Enrollment:</mark> This is a web interface that connects to CA in order to allow users to submit certificate requests, retrieve issued certificates, and download the certificate chain.&#x20;
3. <mark style="color:orange;">Online Responder:</mark> This will receive and respond to individual user requests to verify the status of digital certificates.
4. <mark style="color:orange;">Network Device Enrollment Service:</mark> This service allows non- domain-joined network devices to obtain certificates.
5. <mark style="color:orange;">Certificate Enrollment Web Service:</mark> This role service works with the Certificate Enrollment Policy Web Service, and allows users and computers to perform certificate enrollment using HTTPS. It also allows certificate enrollment for domain computers or devices that are not connected to the domain, and computers or devices that are not part of the domain.
6. <mark style="color:orange;">Certificate Enrollment Policy Web Service:</mark> This publishes the certificate enrollment policy information to users and computers.

## <mark style="color:red;">FSMO roles</mark>

There are five flexible single master operations roles in the Active Directory infrastructure.

&#x20;Each of them perform specific Active Directory tasks that other domain controllers in the infrastructure are not permitted to perform. These five FSMO roles are divided into two categories based on their operation boundaries:

| Forest level                    | Domain level                                                   |
| ------------------------------- | -------------------------------------------------------------- |
| Schema operations master        | The primary domain controller (PDC) emulator operations master |
| Domain-naming operations master | The relative identifier (RID) operations master                |
| N/A                             | The infrastructure operations master                           |

When we create the first Active Directory forest and the first Active Directory domain, all these FSMO roles will be installed in the domain's first domain controller.

### <mark style="color:orange;">Moving FSMO roles</mark>

we need to check the current FSMO role holder:

```
netdom query fsmo
```

move FSMO roles:

```
Move-ADDirectoryServerOperationMasterRole -Identity NEW-FSMO-DC2 -OperationMasterRole PDCEmulator, RIDMaster, InfrastructureMast
```

Once the move is completed, we can check the role owners again.

If we need to move all five FSMO roles to a new host, we can use the following command:

```
Move-ADDirectoryServerOperationMasterRole -Identity REBEL-SDC02 -OperationMasterRole SchemaMaster, DomainNamingMaster, PDCEmula
```

## <mark style="color:red;">Schema operations master</mark>

This role boundary is the forest. This means that an Active Directory forest can have only one schema master. The owner of this role is the only domain controller in the forest who can update the Active Directory schema. In order to make schema changes in the forest, it also needs to have a user account that is a member of the Schema Admins group. Once the schema changes are done from the schema master role owner, those changes will be replicated to other domain controllers in the forest

In an Active Directory forest, the schema master role owner can be found using the following command:

```
Get-ADForest | select SchemaMaster
```

## <mark style="color:red;">Domain-naming operations master</mark>

The domain-naming operations master role holder is responsible for adding and removing domains controllers to and from the Active Directory forest. In the Active Directory forest, the domain-naming operations master role owner can be found using the following command:

```
Get-ADForest | select DomainNamingMaster
```

When you add or remove a domain controller, it will contact the domain- naming operations master role holder via the Remote Procedure Call (RPC) connection, and if it fails, it will not allow you to add or remove the domain controller from the forest. This is a forest-wide role, and only one domain-naming operations master role holder can exist in one forest.

## <mark style="color:red;">Primary domain controller emulator operations master</mark>

The primary domain controller (PDC) operations master role is a domain- wide setting, which means each domain in the forest will have a PDC operations master role holder. PDC is responsible for time synchronization.

In an Active Directory environment, it allows a maximum of a 5-minute time difference (time skew) between server and client to maintain successful authentication. If it's more than 5 minutes, devices will not be able to be added to the domain, users will not be able to authenticate, and the Active Directory-integrated application will start throwing authentication-related errors.

![](<../../../.gitbook/assets/image (275) (1).png>)

the PDC role holder is also responsible for maintaining password change replications. Also, in the event of authentication failures, PDC is responsible for locking down the account. All the passwords changed in other domain controllers will be reported back to the PDC role holder. If any authentication failure occurs in a domain controller before it passes the authentication failure message to the user, itwill check the password saved in the PDC, as that will prevent errors that can occur due to password replication issues.

In the Active Directory domain, the PDC role owner can be found using the following command:

```
Get-ADDomain | select PDCEmulator
```

The PDC is also responsible for managing the Group Policy Object (GPO) edit. Every time a GPO is viewed or updated, it will be done from the copy stored in the PDC's `SYSVOL` folder.

## <mark style="color:red;">Relative ID operations master role</mark>

The relative identifier (RID) master role is a domain-wide setting, and each domain in the forest can have RID role owners. It is responsible for maintaining a pool of relative identifiers that will be used when creating objects in the domain. Each and every object in a domain has a unique security identifier (SID).&#x20;

The RID value is used in the process of SID value creation. The SID is a unique value to represent an object in Active Directory. The RID is the incremental portion of the SID value. Once the RID value is being used to generate a SID, it will not be used again. Even after deleting an object from AD, it will not able to reclaim the RID value back. This ensure the uniqueness of the SID value.

&#x20;The RID role owner maintains a pool of RIDs. When the domain has multiple domain controllers, it will assign a block of 500 RID values for each domain controller. When they are used more than 50%, domain controllers will request another block of RIDs for the RID role owner

In the Active Directory domain, the RID role owner can be found using the following command:

```
Get-ADDomain | select RIDMaster
```

![](<../../../.gitbook/assets/image (276).png>)

## <mark style="color:red;">Infrastructure operations master</mark>

This role is also a domain-wide setting, and it is responsible for replicating SID and distinguished name (DN) value changes to cross-domains.&#x20;

SID and DN values get changed based on their location in the forest. So, if objects are moved, their new values need to be updated in groups and ACLs located in different domains. This is taken care of by the infrastructure operations master. This will ensure that the changed objects have access to their resources without interruptions.

In the Active Directory domain, the infrastructure operations master role owner can be found using the following command:

```
Get-ADDomain | select InfrastructureMaster
```
