# 🔴 Adversary Emulation

## <mark style="color:red;">Cyber Kill Chain (Lockheed Martin)</mark>

> Developed by Lockheed Martin, **the Cyber Kill Chain®** framework is part of the [**Intelligence Driven Defense®**](https://www.lockheedmartin.com/en-us/capabilities/cyber/intelligence-driven-defense.html) model for identification and prevention of cyber intrusions activity. The model identifies what the adversaries must complete in order to achieve their objective.

{% embed url="https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html" %}

![](<../.gitbook/assets/image (53) (1).png>)

## <mark style="color:red;">Cyber Kill Chain (Dell Secure Works)</mark>

Prior to the first version of the kill chain described by Lockheed Martin, Dell Secure Works has developed a more detailed version of the kill chain :

![](<../.gitbook/assets/image (50).png>)

## <mark style="color:red;">Unified Kill Chain</mark>

> The Unified Kill Chains provides insight into the tactics that hackers employ to attain these objectives. This provides a solid basis to develop (or realign) defensive strategies to raise cyber resilience.

{% embed url="https://www.unifiedkillchain.com" %}

![](<../.gitbook/assets/image (54).png>)

#### <mark style="color:green;">The main difference between unified kill chain and cyber kill chain is that the unified kill chain was developed through a hybrid research approach, combining design science with qualitative research methods. this kill chain is a combination of MITRE ATT\&CK framework and Lockheed Martin cyber kill chain.</mark>

## <mark style="color:red;">MITRE ATT\&CK Framework</mark>

> MITRE ATT\&CK® is a globally-accessible knowledge base of adversary tactics and techniques based on real-world observations. The ATT\&CK knowledge base is used as a foundation for the development of specific threat models and methodologies in the private sector, in government, and in the cybersecurity product and service community.

{% embed url="https://attack.mitre.org/matrices/enterprise" %}

{% embed url="https://github.com/mitre-attack/attack-navigator" %}

#### <mark style="color:green;">ATT\&CK can help blue team operators detect, mitigate and respond to adversary threats and APT groups. it also aides the incident response team to detect and stop the threat actor by analyzing their behavior based on known tactics, techniques and procedures (ATT\&CK ID).</mark>

![](<../.gitbook/assets/image (56).png>)

## <mark style="color:red;">TTPs</mark>

MITRE ATT\&CK categorizes adversary behavior by TTPs (tactics, techniques and procedures).

### <mark style="color:orange;">Tactics</mark>&#x20;

The 14 tactics can be summarized as follows:

1. <mark style="color:green;">**Reconnaissance**</mark> <mark style="color:green;"></mark><mark style="color:green;"></mark> - Collecting information from the target organization to prepare future adversarial activities.
2. <mark style="color:green;">**Resource Development**</mark> <mark style="color:green;"></mark><mark style="color:green;"></mark> - Acquiring infrastructure and resources to support adversarial activities against the target organization.
3. <mark style="color:green;">**Initial Access**</mark> - Gaining initial access to the target network.
4. **Execution** - Techniques for running malicious code on the network, usually to explore or steal data.
5. <mark style="color:green;">**Persistence**</mark> - Maintaining access to the target network over time by circumventing measures like credential changes or restarts that could interrupt access.
6. <mark style="color:green;">**Privilege Escalation**</mark> - Gaining administrator or other high-level permissions on the target network.
7. <mark style="color:green;">**Defense Evasion**</mark> - Avoiding detection by security software and IT security teams.
8. <mark style="color:green;">**Credential Access**</mark> - Stealing account names and passwords, allowing the adversary to circumvent security measures by accessing the network with legitimate credentials.
9. <mark style="color:green;">**Discovery**</mark> - Exploring the network and collecting information, such as which applications and services are running, what accounts exist, what resources are available, etc.
10. <mark style="color:green;">**Lateral Movement**</mark> - Accessing and controlling remote services on the target network.
11. <mark style="color:green;">**Collection**</mark> - Aggregating data from a variety of sources on the target network.
12. <mark style="color:green;">**Command and Control**</mark> - Techniques for communicating with systems under the adversary’s control within the target network.
13. <mark style="color:green;">**Exfiltration**</mark> <mark style="color:green;"></mark><mark style="color:green;"></mark> - Techniques for stealing data from the target network and transferring it to an external server controlled by the adversary.
14. <mark style="color:green;">**Impact**</mark> - Techniques for destroying data or disrupting the availability of applications, services, or the target network itself.

Knowing these tactics will help us in :&#x20;

1. Cyber Threat Intelligence
2. Threat Intelligence & Analytics&#x20;
3. Penetration Testing & Adversary Emulation
4. Threat Coverage Gap Assessment

### <mark style="color:orange;">Techniques</mark>

Adversarial techniques answer “how” an adversary attains a tactical objective, and the course of action they take to get what they seek.

## <mark style="color:red;">ATT\&CK Use Cases</mark>

<mark style="color:green;">Threat Intelligence :</mark> Cyber threat intelligence is all about understanding the cyber threat groups that matter to your organization, including their motives, typical targets, behaviors, and preferred software/techniques. IT security teams can use the MITRE ATT\&CK framework to access specific information on the behaviors of known threat groups, then identify strategies to detect and mitigate their preferred techniques.

<mark style="color:green;">Threat Detection & Analytics :</mark> Each technique in the MITRE ATT\&CK framework includes a metadata field called “Data Sources”. This field lists specific types of data that organizations should collect to gain the visibility needed to detect that technique.



<mark style="color:green;">Penetration Testing & Adversary Emulation :</mark> Once your security team writes an analytic or configures security monitoring to detect an adversarial technique, penetration testing or adversary emulation can be used to evaluate the effectiveness of the implemented threat detection measures.

<mark style="color:green;">Threat Coverage Gap Assessment  :</mark>  IT security teams can map existing threat detection capabilities onto the MITRE ATT\&CK framework to identify gaps in their defenses. They can identify the cyber threat groups which are most likely to target them and compare their threat coverage to the preferred techniques used by those organizations.

## <mark style="color:red;">Adversary Emulation Frameworks</mark>&#x20;

Adversary emulation, also commonly referred to as Red Team exercises, is meant to provide comprehensive and real-world conditions that demonstrate substantial risks posed by adversaries operating today.

### <mark style="color:orange;">Atomic Red Team</mark>

> Atomic Red Team™ is library of tests mapped to the [MITRE ATT\&CK®](https://attack.mitre.org) framework. Security teams can use Atomic Red Team to quickly, portably, and reproducibly test their environments.

{% embed url="https://github.com/redcanaryco/atomic-red-team" %}

#### <mark style="color:green;">Usage:</mark>&#x20;

Atomic Red Team will run APT tactics described in MITRE ATT\&CK framework based on category and IDs. these tests can be done manually or automatically using [https://github.com/redcanaryco/invoke-atomicredteam/wiki](https://github.com/redcanaryco/invoke-atomicredteam/wiki).

{% embed url="https://youtu.be/eHZEyEf_icE?t=1840" %}

### <mark style="color:orange;">Caldera</mark>

> CALDERA™ is a cyber security platform designed to easily automate adversary emulation, assist manual red-teams, and automate incident response.

#### Caldera has a C2-like design. it consists of a C2 server and multiple agents for deployment on the test target. after placing the agents in the system, the C2 can run remote commands and/or test ATT\&CK tactics and techniques on agents.

{% embed url="https://github.com/mitre/caldera" %}

### <mark style="color:orange;">APT Simulator</mark>

> A toolset to make a system look as if it was the victim of an APT attack.

#### Its a windows batch script that uses a set of tools and output files to make a system look as if it was compromised. the blue team can then analyze the system and test end-point detection systems/sensors or monitoring detection capabilities. it can also provide and environment for practicing digital forensics.

{% embed url="https://github.com/NextronSystems/APTSimulator" %}

### <mark style="color:orange;">Flightsim</mark>

> **flightsim** is a lightweight utility used to generate malicious network traffic and help security teams to evaluate security controls and network visibility. The tool performs tests to simulate DNS tunneling, DGA traffic, requests to known active C2 destinations, and other suspicious traffic patterns.

{% embed url="https://github.com/alphasoc/flightsim" %}
