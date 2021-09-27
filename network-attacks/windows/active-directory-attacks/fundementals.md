# Fundementals

## Active Directory Components

![](../../../.gitbook/assets/image%20%28168%29.png)

### \*\*\*\*

### **Forests** 

Represents a complete Active Directory instance. **It is made of one or more domains and domain trees.** Each domain has its own characteristics, boundaries, and resources. But at the same time, it shares a common logical structure, schema, and directory configuration within the forest. Similarly, tribes have a relationship with the forest and other tribes, and **domains in the Active Directory forest will have a two-way trust relationship. The first domain controller in the Active Directory service deployment is the forest root domain.**

{% hint style="info" %}
Two-way transitive trust is a logical link between domains, where the trusting domain honors the logon authentication of the trusted domain. When considering the previous example, users in rebeladminit.com can authenticate into mytraining.ca , and vice versa.

Any object located in a particular domain inherently trusts other objects in other domains in the same forest. This is not the same as when considering authentication between forests. For that, it may \(depending on the trust method\) require additional login credentials. An organization can have a single forest or multiple forests based on the company's business requirements.
{% endhint %}

### 

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



### Domain controllers

**The domain controller is a computer that runs a Windows server operating system, and holds the Active Directory Domain Services role. It can be either a physical server or a virtual server.**The domain controller holds the directory partition that will be replicated to the other domain controllers in the same domain. The domain can have any number of domain controllers. The number of domain controllers is dependent on the enterprise's size, geographical placement, and network segmentation.



### Schema

**The schema is the foundation of object structures for the entire forest**. **Every domain in the forest shares the same set of object structures that are defined in the schema.** If an attacker accesses or modifies the schema, every domain in the forest will be affected. The schema is one-third of the directory database, which is stored on all domain controllers in every domain. **Only one domain controller in the forest can update the schema?the Schema Master.**

\*\*\*\*

### Account Policy

The Account Policy for domain users is established at the domain level. The Account Policy for the domain level includes control over passwords, account lockout, and Kerberos authentication. This means that domain user accounts cannot be controlled at the organizational unit level; they must be controlled at the domain level. Also, the Account Policy is not inherited from the parent domain, if we are focusing in on a child domain. There is no possible way to get a parent domain to push down Account Policies to child domains.  


### Group Policy Objects

GPOs are the main form of pushing out security to computers in the domain. However, GPOs that are configured within a domain do not and cannot span multiple domains by inheritance or hierarchy. The GPOs can be available to other domains, but there is no option to configure GPOs to span domains with a single configuration.

\*\*\*\*

### Delegation

By far one of the most important features of Active Directory is delegation. However, delegation without a solid OU design is almost impossible to implement. OUs need to be designed to delegate administration. The key to delegation is to have the OU contain the objects that the delegate will control. For example, if you have delegated the ability for the HR manager to reset passwords for only the HR employees, then there needs to be an OU for these user accounts. A good design would have an OU named HR\_employees, which contains only the user accounts of the HR employees. The design would have this OU low in the OU hierarchy, so that no other OUs are below it. In that design, the HR manager will not have control over any other user accounts by default.  


### GPO deployment

Many administrators leave out the consideration of GPO deployment when they design OUs. This is a mistake, mainly for security reasons. The GPO deployment should be interwoven with delegation considerations. If there is a conflict between the two design needs, the delegation needs usually win. In this case, the GPO deployment will be taken care of by filtering the GPO \(setting permissions on the GPO\). An example of a typical GPO design would be the configuration of the Internet Explorer proxy settings for a branch office. All employees in the branch office need to have the same proxy settings for IE, which can easily be set by using GPOs. In this case, there would be an OU named Branch1\_employees, which contains the user accounts for only the branch office. This OU would be low in the OU hierarchy, with no other OUs below it.

An error that many companies make is to duplicate their company's organizational chart for their OU design. The OUs are not well suited for this model, since this model usually breaks how the administration of objects and deployment of GPOs are implemented. This is not to say that a small percentage of companies have not successfully used the org chart for the OU design, but in most cases it will cause more anguish than benefit.



### Global catalog server

The global catalog server holds the full writable copy of objects in its host domain, and the partial copy of the objects in other domains in the same forest. The partial replica contains a copy of every object in the forest and the most commonly used attributes in queries. Applications and users in one domain can query for the objects in another domain \(in the same forest\) via the global catalog server. All domain controllers in the domain will not be global catalog servers by default. When installing the first domain controller, it will become the global catalog server.



### Active Directory sites

The Active Directory site defines a physical topology of the network. Sites can be separate buildings in a campus network, with the branch office in a separate city or even in a separate country.



## Active Directory server roles

There are five main Active Directory server roles**:**

* Active Directory Domain Services \(AD DS\) 
* Active Directory Federation Services \(AD FS\) 
* Active Directory Lightweight Directory Services \(AD LDS\) 
* Active Directory Rights Management Services \(AD RMS\) 
* Active Directory Certificate Services \(AD CS\)



### AD DS

AD DS can manage an organization's resources in a secure, efficient manner, and helps to organize objects in a hierarchical structure.



### AD FS

allows you to share identities between trusted identity infrastructures and is based on a claim-based authorization \(CBA\) mechanism. It will trust identities from completely different identity infrastructures, and pass identity information as claims to the organization that hosts the applications. Then, the company that hosts the application will map these claims to claims that the application understands, and make the authorization decisions.



### ADLDS

Some applications require a directory-enabled environment in order to operate. But there is no need to be in a fully-blown Active Directory environment. Microsoft developed AD LDS to enable data storage and retrieval for directory-enabled applications, without the dependencies that are required for AD DS.



### AD RMS

AD RMS help organizations to protect sensitive data from unauthorized access. ​​AD RMS can be used to encrypt managed identities and apply authorization policies to your files, emails, and presentations. This will prevent files from being copied, forwarded, or printed by unauthorized people. This also allows file expiration, which will prevent users from viewing the data of a document beyond a specified period of time.



### AD CS

AD CS helps organizations build public key infrastructure \(PKI\) in an easy, cost-effective way. Digital certificates issued by the certification authority can be used to authenticate users, computers, and devices. The certification authority is responsible for receiving certificate requests, verifying certificate requests, and issuing, renewing, and revoking certificates.



{% hint style="info" %}
In a small  active directory environment the domain controller \(the server with the AD DS role \) usually acts as the DNS server as well. also when we create a new forest \(the first domain controller\) we have to have the DNS service installed on the root server as well.
{% endhint %}



