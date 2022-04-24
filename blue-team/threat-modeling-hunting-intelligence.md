# 🔴 Threat Modeling / Hunting / Intelligence

## <mark style="color:red;">Resources</mark>

{% embed url="https://github.com/fabacab/awesome-cybersecurity-blueteam" %}

{% embed url="https://github.com/maldevel/blue-team/tree/master/hardening" %}

## <mark style="color:red;">Checklists & Standards</mark>

### <mark style="color:orange;">NIST Checklist Repository</mark>

A repository for various security checklists/benchmarks.

{% embed url="https://ncp.nist.gov/repository" %}

### <mark style="color:orange;">STIGs</mark>&#x20;

Security Technical Implementation Guides (STIGs) are a series of cybersecurity requirements for IT products deployed within DoD agencies. STIGs are the source of configuration guidance for network devices, software, databases and operating systems. The aim is to lower the risk of cybersecurity threats, breaches and intrusion by making the set-up of the network as secure as possible.

{% embed url="https://stigviewer.com/stigs" %}

### <mark style="color:orange;">Microsoft Security Baselines</mark>

{% embed url="https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-security-configuration-framework/windows-security-baselines" %}

## <mark style="color:red;">Threat Modeling</mark>

Threat modeling or attack surface modeling works to identify, communicate, and understand threats and mitigations within the context of protecting something of value.

A threat model is a structured representation of all the information that affects the security of an application. In essence, it is a view of the application and its environment through the lens of security.

Threat modeling can be applied to a wide range of things, including software, applications, systems, networks, distributed systems, Internet of Things (IoT) devices, and business processes.

<mark style="color:green;">Thread modeling four question framework:</mark>

* What are we working on?
* What can go wrong?
* What are we going to do about it?
* Did we do a good job?

## <mark style="color:red;">Models</mark>

![](<../.gitbook/assets/image (29).png>)

### <mark style="color:orange;">DREAD Model</mark>

#### DREAD stands for Damage, Reproducibility, Exploitability, Affect and Discovery.&#x20;

The DREAD model is a form of quantitative risk analysis that involves rating the severity of a cyber threat. When you encounter a cyber threat in your business’s information technology (IT) infrastructure, you can use the DREAD model to determine how much damage it has already caused and can cause in the future.

&#x20;You must assess various key points of the cyber threat while assigning a numbered rating to each of these points. When finished, you can then compare the total rating to that of the DREAD model’s rating system, which should reveal whether the cyber threat has a low, medium or high risk to your business.

#### <mark style="color:green;">Key Points :</mark>

1. **Damage (10)**: What’s the total amount of damage the cyber threat is capable of causing your business?
2. **Reproducibility (9)**: How easily can other hackers replicate the cyber threat?
3. **Exploitability (8)**: How much time and energy is required to exploit the threat and, thus, perform a cyber attack against your business?
4. **Affected Users (10)**: How many people, either inside or outside of your business, will be affected by the cyber threat?
5. **Discoverability (10)**: Can you easily discover the cyber threat?

### <mark style="color:orange;">STRIDE Model</mark>

#### STRIDE stands for Spoofing, Tampering, Reputation, Information disclosure, DoS and Escalation of privileges.

STRIDE was developed in the late 1990’s by two engineers working at Microsoft, Koren Kohnfelder and Praerit Garg. STRIDE’s threat model accounts for six different threat categories:

![](<../.gitbook/assets/image (40).png>)

### <mark style="color:orange;">PASTA Model</mark>

#### PASTA means "process for attack simulation and threat analysis".

An open-source risk-centric threat modeling methodology that provides a step-by-step process to inject risk analysis and context into an organization’s overall security strategy from the beginning. PASTA encourages collaboration across all stakeholders, creating an environment focused on security.

PASTA has seven stages, with each stage acting as building blocks to one another. This approach allows your threat model to be a linear process and leverage existing security testing activities present within your organization, like code review, third party library analysis, static analysis, and threat monitoring for application infrastructure.

![](<../.gitbook/assets/image (48).png>)

#### <mark style="color:green;">7 steps of PASTA :</mark>

1. **Define the Objectives** : Focus on what is important to your business. Understand the objectives of each application or product. Objectives may be driven internally or they may be influenced by external partners, clients, or regulatory frameworks.
2. **Define the tactical scope of assets and components** :  Understand the attack surface, and create a picture of what it is that you are protecting. For each business component identify how they are configured, what dependencies they have on other internal applications, or where third party applications are used.
3. **Application decomposition and identify application controls :**   Map the relationships between components. Identify users and their roles and permissions, assets, data, services, hardware, and software. Understand where implicit trust models are in place which could be ripe for exploitation, and the application controls that protect high risk web transactions that could become targets for attack.
4. **Threat analysis based on threat intelligence** :  Research and find the credible threats that affect your industry and products, and build a threat library. Utilise intelligence to understand the latest threats affecting your industry or products, and analyse application logs to understand the behaviours the system is recording, including attacks that existing protections have mitigated.
5. **Vulnerability detection :**  Map which weaknesses will break under threats. This stage builds on stage 2 which identified the attack surface, and looks for vulnerabilities, design flaws, and weaknesses in the codebase, system configuration, or architecture.
6. **Analyze and model attacks :**  The aim is to emulate the attacks that could exploit any identified weaknesses or vulnerabilities, and prove that the suspected risks to applications actually are risks. The PASTA threat modelling methodology recommends building attack trees, which map threats, attacks and vulnerabilities, to create a blueprint for how applications can be exploited.
7. **Risk/impact analysis and development of countermeasures :**  This stage uses the answers from earlier stages, such as what’s important to the organisation (stage 1), what are we working with (stage 2), how do they all work together (stage 3), and what does my threat intelligence tell me about our risks (stage 4) in order to create countermeasures

### <mark style="color:orange;">LINDDUN Model</mark>

LINDDUN is short for linkability, identifiability, nonrepudiation, detectability, disclosure of information, unawareness, noncompliance.

focuses on privacy concerns and can be used for data security. Consisting of six steps, LINDDUN provides a systematic approach to privacy assessment.

![](<../.gitbook/assets/image (17).png>)

LINDDUN starts with a DFD of the system that defines the system's data flows, data stores, processes, and external entities. By systematically iterating over all model elements and analyzing them from the point of view of threat categories, LINDDUN users identify a threat's applicability to the system and build threat trees.

### <mark style="color:orange;">CVSS Model</mark>

[The Common Vulnerability Scoring System (CVSS)](https://www.first.org/cvss/specification-document) captures the principal characteristics of a vulnerability and produces a numerical severity score. CVSS was developed by [NIST](https://www.nist.gov) and is maintained by the [Forum of Incident Response and Security Teams (FIRST)](https://www.first.org) with support and contributions from the [CVSS Special Interest Group](https://www.first.org/cvss/v2/meetings). The CVSS provides users a common and standardized scoring system within different cyber and cyber-physical platforms. A CVSS score can be computed by a calculator that is [available online](https://nvd.nist.gov/vuln-metrics/cvss).

CVSS consists of three metric groups (Base, Temporal, and Environmental) with a set of metrics in each.

![](<../.gitbook/assets/image (49).png>)

A CVSS score is derived from values assigned by an analyst for each metric. The metrics are explained extensively in the documentation. The CVSS method is often used in combination with other threat-modeling methods.

### <mark style="color:orange;">Attack Trees</mark>

Using attack trees to model threats is one of the oldest and most widely applied techniques on cyber-only systems, cyber-physical systems, and purely physical systems.&#x20;

Attack trees are diagrams that depict attacks on a system in tree form. The tree root is the goal for the attack, and the leaves are ways to achieve that goal. Each goal is represented as a separate tree. Thus, the system threat analysis produces a set of attack trees.

![](<../.gitbook/assets/image (38).png>)

In the case of a complex system, attack trees can be built for each component instead of for the whole system. Administrators can build attack trees and use them to inform security decisions, to determine whether the systems are vulnerable to an attack, and to evaluate a specific type of attack.

### <mark style="color:orange;">**Persona non Grata**</mark>

Persona non Grata (PnG) focuses on the motivations and skills of human attackers. It characterizes users as archetypes that can misuse the system and forces analysts to view the system from an unintended-use point of view.

The idea is to introduce a technical expert to a potential attacker of the system and examine the attacker's skills, motivations, and goals. This analysis helps the expert understand the system's vulnerabilities from the point of view of an attacker.

### <mark style="color:orange;">**Security Cards**</mark>

Security Cards identify unusual and complex attacks. They are not a formal method but, rather, a kind of brainstorming technique. With help from a deck of cards (see an example in Figure 6), analysts can answer questions about an attack, such as

* Who might attack?
* Why might the system be attacked?
* What assets are of interest?
* How can these attacks be implemented?"

This method uses a deck of 42 cards to facilitate threat-discovery activities: Human Impact (9 cards), Adversary's Motivations (13 cards), Adversary Resources (11 cards), and Adversary's Methods (9 cards).

![](<../.gitbook/assets/image (52).png>)

### <mark style="color:orange;">hTMM Model</mark>

#### hTMM stands for hybrid threat modeling method.

Presents a hybrid method of threat modeling that attempts to meld the desirable features of three methods: Security Cards, Persona non Grata, and STRIDE.

It consists of a combination of SQUARE (Security Quality Requirements Engineering Method), Security Cards, and PnG activities.&#x20;

{% file src="../.gitbook/assets/Hybrid Thread Modeling Method.pdf" %}

## <mark style="color:red;">**Threat Modeling Tools**</mark>

{% embed url="https://cairis.org" %}

{% embed url="https://www.iriusrisk.com" %}

{% embed url="https://www.kennasecurity.com/products/vm" %}

{% embed url="https://docs.microsoft.com/en-us/azure/security/develop/threat-modeling-tool" %}

{% embed url="https://owasp.org/www-project-threat-dragon" %}

{% embed url="https://www.securitycompass.com/sdelements" %}

{% embed url="https://foreseeti.com/securicad-enterprise" %}

{% embed url="https://threagile.io" %}

{% embed url="https://threatmodeler.com" %}

{% embed url="https://www.tutamantic.com" %}

## <mark style="color:red;">Threat Hunting</mark>

#### Threat hunting is a proactive approach to identifying previously unknown or ongoing non-remediated threats within an organizations network.

## <mark style="color:red;">Hunting Maturity Model</mark>

#### The Hunting Maturity Model (HMM) is a simple model for evaluating an organization's threat hunting capability. It provides not only a "where are we now?" metric, but also a roadmap for program improvement.

The maturity model has 4 levels by which the maturity of threat hunters is determined :&#x20;

* **HMM 0 (initial)** - automated alerting, limited or no routing data collection
* **HMM 1 (minimal)** - threat intelligence indicator searches and moderate or high level data collection
* **HMM 2 (procedural)** - data analysis procedures created by others, high or very high data collection
* **HMM 3 (innovative)** - create new data analysis procedures, high or very high data collection
* **HMM 4 (leading)** - automate the majority of successful data analysis procedures and high or very high data collection

## <mark style="color:red;">SANS sliding scale of cyber security</mark>

The Sliding Scale of Cyber Security is a model for providing a nuanced discussion to the categories of actions and investments that contribute to cyber security.

{% embed url="https://www.sans.org/white-papers/36240" %}

* Architecture
* Passive Defense
* Intelligence
* Offense

## <mark style="color:red;">Threat Intelligence</mark>

#### Threat intelligence is the analysis of data using tools and techniques to generate meaningful information about existing or emerging threats targeting the organization that helps mitigate risks.

This information may include:

* Mechanisms of an attack
* How to identify that an attack is happening
* Ways different types of attacks might affect the business
* Action-oriented advice about how to defend against attacks&#x20;

#### Types of threat intelligence:

* <mark style="color:green;">**Strategic Threat Intelligence :**</mark> <mark style="color:green;"></mark><mark style="color:green;"></mark> provides an overview of the organization’s threat landscape. It is less technical is mainly for executive-level security professionals to drive high-level organizational strategy based on the findings in the reports. Ideally, strategic threat intelligence provides insights like vulnerabilities and risks associated with the organization’s threat landscape with preventive actions, threat actors, their goals, and the severity of the potential attacks.
* <mark style="color:green;">**Tactical Threat Intelligence :**</mark>  consists of more specific details on threat actors TTP and is mainly for the security team to understand the attack vectors. Intelligence gives them insights on how to build a defense strategy to mitigate those attacks. The report includes the vulnerabilities in the security systems that attackers could take advantage of and how to identify such attacks.
* <mark style="color:green;">Operational Threat Intelligence :</mark>  focuses on knowledge about the attacks. It gives detailed insights on factors like nature, motive, timing, and how an attack is carried out. Ideally, the information is gathered from hacker chat rooms or their discussion online through infiltration, which makes it difficult to obtain.

{% embed url="https://youtu.be/7v2O-uEK2VM" %}

## <mark style="color:red;">Tactical Intelligence</mark>

### <mark style="color:orange;">CTI Pyramid of Pain</mark>

#### represents the amount of pain you can inflict on advanced adversaries.

If you are going to use the only hash value, IP address for threat detection, attackers going to step ahead of you, they can easily change the hash value and IP address and use different tools and techniques to attain their motive. So try to find as many as details like attacker tools, techniques. These are all difficult for an attacker to change from one attack to another.

![](<../.gitbook/assets/image (11).png>)

By fetching all the details on the pyramid, we are on the track and expertise in threat intelligence and it will be useful to get the full picture of the threat and threat actors.

#### <mark style="color:green;">**CTI Life cycle:**</mark>

* **Planning**– Determine the purpose,objective and requirements of CTI.
* **Collection**– Collecting data from various sources
* **Processing-** Process the collected information and make it ready for anlaysis.
* **Analysis**– Analysing the data and transforming it into intelligence and making it ready for sharing.
* **Dissemination**– Sharing threat intellegince data
* **Feedback**– Acquire and analyse stakeholder feedbacks.

### <mark style="color:orange;">Resources</mark>

{% embed url="https://otx.alienvault.com" %}

{% embed url="https://www.threatcrowd.org" %}

{% embed url="https://www.misp-project.org" %}

## <mark style="color:red;">Comparison</mark>

### <mark style="color:orange;">Threat Intelligence vs Threat Hunting</mark>

| Threat Hunting                                                          | Threat Intelligence                                            |
| ----------------------------------------------------------------------- | -------------------------------------------------------------- |
| track APT activities inside your own organization                       | track global APT activities, news, known bads                  |
| monitoring and analysis of suspicious activities/events                 | monitoring and analysis of breach reports and global incidents |
| detect the adversary in action, sop their advance, prevent exfiltration | knowing the adversary beforehand, early warning and mitigation |

### <mark style="color:orange;">Threat Hunting vs Real-time Detection</mark>

| Real-time Detection                                              | Threat Hunting                                                  |
| ---------------------------------------------------------------- | --------------------------------------------------------------- |
| relies on known bads                                             | hunt environment for unknown bads                               |
| generates alerts that are to be further investigated by analysts | generates confirmed incidents which trigger incident response   |
| typically highly automated                                       | can partially leverage automation but majority is manual effort |
| can leverage IoCs                                                | can generate IoCs                                               |

{% embed url="https://github.com/Neo23x0/Loki" %}
