# 🔧 AD Components

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

![](<../../.gitbook/assets/image (281).png>)

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



















