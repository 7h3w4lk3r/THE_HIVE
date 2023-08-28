# 🔵 Security Architecture

{% hint style="warning" %}
#### Security architecture is a huge topic and can not be covered in any  single document. it requires familiarity with a broad range of network devices, security appliances and their limitations/capabilities to make the best choices.&#x20;

#### Designing a secure network is scenario-based and is not  guided in any industry standard but there are some best practice advices and general concepts to it.

#### This section is about some tips and things to consider in network architecture and designing a defensible infrastructure.
{% endhint %}

#### <mark style="color:green;">Types of security architecture:</mark>

* Enterprise Security Architecture (strategic & program management)
* Solution Security Architecture (technology project focused)
* Security Engineering (technology implementation focused)

## <mark style="color:red;">Defensible Design</mark>

<mark style="color:green;">**Characteristics of a defensible network architecture:**</mark>

* Monitored (IDS/IPS/NSM)
* Inventored (Knowing every host and application)
* Controlled (ingress & egress analysis)
* Claimed (ownership identification)
* Minimized (reduced attack surface)
* Assessed (vulnerability assessment, pentesting, red team engagement)

### <mark style="color:orange;">Failed Mindsets</mark>

* <mark style="color:green;">The LAN is Secure</mark> <mark style="color:red;">(insider attacks, phishing, physical access)</mark>
* <mark style="color:green;">Perimeter-based Security Model</mark> <mark style="color:red;">(in-depth compromise, flat network is always vulnerable)</mark>
* <mark style="color:green;">The all-prevent Defense Mindset</mark> <mark style="color:red;">(prevention and control is critical but defense-in-depth is a must)</mark>
* <mark style="color:green;">Compliance-driven Security</mark> <mark style="color:red;">(fails in the face of an APT)</mark>
* <mark style="color:green;">We always did it that way</mark> <mark style="color:red;">(you did it wrong)</mark>
* <mark style="color:green;">Introducing Technology without Analysis</mark> <mark style="color:red;">(unexpected new attack surface)</mark>

### <mark style="color:orange;">Security Architecture Considerations</mark>&#x20;

* Network defenses should always operate under presumption of compromise
* Preventive controls will fail in the face of a persistent adversary
* Know thy network
* [Prioritize critical assets](https://insights.sei.cmu.edu/blog/cybersecurity-architecture-part-2-system-boundary-and-boundary-protection/)
* Architecting with security operations monitoring in mind.

### <mark style="color:orange;">Security Architecture Anti-patters</mark>

* **When administration of a system is performed from a device which is less trusted than the system being administered.**
* **When layered defenses in a network data plane can be short-cut via the management plane**
* **When the same controls are implemented by two firewalls in series, sometimes from different manufacturers.**
* **When you build - in the public cloud - the solution you would have built in your own data centres.**
* **When a third party has unfettered remote access for administrative or operational purposes, without any constraints or monitoring in place.**
* **When a system cannot be patched due to it needing to remain operational 24/7**

### <mark style="color:orange;">Time-based Security Formula</mark>

#### <mark style="color:green;">prevention time must be longer than detection and reaction time.</mark>

#### <mark style="color:green;">`P>D+R`</mark>

* P : how long our protections last
* D : how long it takes us to detect&#x20;
* R : how long it takes us to respond

### <mark style="color:orange;">Common Frameworks</mark>

1. <mark style="color:green;">**TOGAF:**</mark> The Open Group Architecture Framework, or TOGAF, helps determine what problems a business wants to solve with security architecture. It focuses on the preliminary phases of security architecture, an organization's scope and goal, setting out the problems a business intends to solve with this process. However, it does not give specific guidance on how to address security issues.
2. <mark style="color:green;">**SABSA:**</mark> Sherwood Applied Business Security Architecture, or SABSA, is a quite policy driven framework that helps define key questions that must be answered by security architecture: who, what, when and why. Its aim is to ensure that security services are designed, delivered and supported as an integral part of the enterprise's IT management. However, while often described as a 'security architecture method', it does not go into specifics regarding technical implementation.
3. <mark style="color:green;">**OSA:**</mark> Open Security Architecture, or OSA, is a framework related to functionality and technical security controls. It offers a comprehensive overview of key security issues, principles, components and concepts underlying architectural decisions that are involved when designing effective security architectures. That said, it can typically only be used once the security architecture is already designed.
