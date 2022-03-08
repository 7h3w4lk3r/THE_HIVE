# 🔴 Active Directory

## <mark style="color:red;">Definition</mark> <a href="#definition" id="definition"></a>

Active Directory is a centralized directory service used to manage Windows networks. It stores information about objects on the network and make it easy to configure what is needed.

![](<../../.gitbook/assets/image (24).png>)

## <mark style="color:red;">Intro</mark>

Active Directory attacks is a huge topic, a lot of the scenarios depend on the situation of attacker and network infrastructure. this section of the book is about the known active directory attacks and tips/tricks.

{% hint style="info" %}
Most of the attacks in this section (in real world scenarios as well) are based on the assumption that you already have a low-privilege access to a system inside the internal network or have physically attached your system and you are pass the firewall. normally, you wont see a domain controller out in the open or directly accessible from the internet, so most of the active directory attacks are part of an internal pentest or red team engagement.
{% endhint %}

## <mark style="color:red;">The Process</mark>

#### **Typically the process of Active Directory penetration testing ( aka internal pentesting ) is like this:**

1. **Initial Domain Access ( through a low-privileged user account )**
2. **Local Privilege Escalation**
3. **Internal Domain Recon**
4. **Poisoning / MITM**
5. **Domain Admin Access**
6. **Domain Dominance ( fancy name for " full compromise " )**
7. **Domain Persistence**
8. **Asset Access**
9. **Exfiltration**

## <mark style="color:red;">Active Directory Kill Chain</mark>

![](<../../.gitbook/assets/image (174).png>)

## <mark style="color:red;">Build your own  lab</mark>

{% embed url="https://sec-consult.com/blog/detail/creating-active-directory-labs-for-blue-and-red-teams/" %}

## <mark style="color:red;">Resources</mark>

{% embed url="https://wiki.bufu-sec.com/active_directory/domain_enumeration/domain_acl" %}

{% embed url="https://github.com/cfalta/MicrosoftWontFixList/blob/main/README.md" %}

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#active-directory-attacks" %}

{% embed url="https://adsecurity.org/?page_id=4031" %}

{% embed url="https://github.com/Integration-IT/Active-Directory-Exploitation-Cheat-Sheet" %}

{% embed url="https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet#domain-enumeration" %}

## <mark style="color:red;">Tools</mark>

{% embed url="https://github.com/CoreSecurity/impacket" %}

{% embed url="https://github.com/maaaaz/impacket-examples-windows" %}

{% embed url="https://github.com/lgandx/Responder" %}

{% embed url="https://github.com/Kevin-Robertson/InveighZero" %}

{% embed url="https://github.com/gentilkiwi/mimikatz" %}

{% embed url="https://github.com/funkandwagnalls/ranger" %}

{% embed url="https://docs.microsoft.com/en-us/sysinternals/downloads/adexplorer" %}

{% embed url="https://github.com/byt3bl33d3r/CrackMapExec" %}

{% embed url="https://github.com/fox-it/mitm6.git" %}

{% embed url="https://github.com/sense-of-security/ADRecon" %}

{% embed url="https://github.com/sense-of-security/ADRecon" %}

{% embed url="https://github.com/hausec/ADAPE-Script" %}

{% embed url="https://github.com/vletoux/pingcastle" %}

{% embed url="https://github.com/ropnop/kerbrute" %}

{% embed url="https://github.com/GhostPack/Rubeus" %}

{% embed url="https://posts.specterops.io/introducing-bloodhound-4-0-the-azure-update-9b2b26c5e350" %}

{% embed url="https://github.com/BloodHoundAD/BloodHound" %}

{% embed url="https://github.com/NetSPI/PowerUpSQL" %}

{% embed url="https://github.com/Kevin-Robertson/Powermad" %}
