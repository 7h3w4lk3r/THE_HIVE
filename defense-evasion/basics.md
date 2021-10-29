# Basics

## Detection Techniques

An antivirus tool is an essential component of most anti-malware suites. It must identify known and previously unseen malicious files with the goal of blocking them before they can cause damage. Though tools differ in the implementation of malware-detection mechanisms, they tend to incorporate the same virus detection techniques. Familiarity with these techniques can help you understand how antivirus software works.

Malware detection techniques employed by antivirus tools can be classified as follows:

**Signature-based detection** uses key aspects of an examined file to create a static fingerprint of known malware. The signature could represent a series of bytes in the file. It could also be a cryptographic hash of the file or its sections. This method of detecting malware has been an essential aspect of antivirus tools since their inception; it remains a part of many tools to date, though its importance is diminishing. A major limitation of signature-based detection is that, by itself, this method is unable to flag malicious files for which signatures have not yet been developed. With this in mind, modern attackers frequently mutate their creations to retain malicious functionality by changing the file’s signature.

**Heuristics-based detection** aims at generically detecting new malware by statically examining files for suspicious characteristics without an exact signature match. For instance, an antivirus tool might look for the presence of rare instructions or junk code in the examined file. The tool might also emulate running the file to see what it would do if executed, attempting to do this without noticeably slowing down the system. A single suspicious attribute might not be enough to flag the file as malicious. However, several such characteristics might exceed the expected risk threshold, leading the tool to classify the file as malware. The biggest downside of heuristics is it can inadvertently flag legitimate files as malicious.

**Behavioral detection** observes how the program executes, rather than merely emulating its execution. This approach attempts to identify malware by looking for suspicious behaviors, such as unpacking of malcode, modifying the hosts file or observing keystrokes. Noticing such actions allows an antivirus tool to detect the presence of previously unseen malware on the protected system. As with heuristics, each of these actions by itself might not be sufficient to classify the program as malware. However, taken together, they could be indicative of a malicious program. The use of behavioral techniques brings antivirus tools closer to the category of host intrusion prevention systems (HIPS), which have traditionally existed as a separate product category.

**Cloud-based detection** identifies malware by collecting data from protected computers while analyzing it on the provider's infrastructure, instead of performing the analysis locally. This is usually done by capturing the relevant details about the file and the context of its execution on the endpoint, and providing them to the cloud engine for processing. The local antivirus agent only needs to perform minimal processing. Moreover, the vendor's cloud engine can derive patterns related to malware characteristics and behavior by correlating data from multiple systems. In contrast, other antivirus components base decisions mostly on locally observed attributes and behaviors. A cloud-based antivirus engine allows individual users of the tool to benefit from the experiences of other members of the community.

**Sandboxing ** detect malware by testing potentially malicious code in an isolated virtual environment. This allows researchers to observe the code’s real behavior in a safe environment, where it cannot spread or do any harm to the system and network it’s running on. Sandboxing is a useful malware detection technique, as it is able to determine, in great detail, how files really act in environments. It can also provide in-depth information on behaviors, allowing analysts to figure out what the intentions of a given threat were. However, the sandbox method has some significant drawbacks. Modern threat actors create “sandbox-aware” malware that knows when it’s being run in a sandbox, and, therefore, acts differently than it would in a real environment in order to avoid being flagged. Additionally, some malware variants are built to take advantage of the blind spots in sandboxes. Sandboxing also creates some performance challenges; because it is time consuming, sandboxing every file is not possible.

**Application Whitelisting,** While signatures are considered blacklisting, another approach to blocking malware is whitelisting. Whitelisting validates and controls all aspects of what a process is allowed to do, and blocks applications from doing _everything_—except what they’re supposed to do. This is beneficial for blocking threats such as zero-days, yet can be very frustrating for end users who want to run completely safe and legitimate applications, but are unable to. This is why application whitelisting should be used in high-risk environments and in kiosks like ATMs. It should be noted that “standard” whitelisting relates to applications (allowed\not allowed), while “smart” whitelisting relates to processes within a legitimate app. For example, Microsoft Word is whitelisted, but the WINWORD.exe process is not allowed to request kernel handles to other processes.

## Recon <a href="recon" id="recon"></a>

Common AV process names:

| Process Name | Vendor/Product                 |
| ------------ | ------------------------------ |
| avp.exe      | Kaspersky Internet Security    |
| cpda.exe     | Check Point End Point Security |
| MsMpEng.exe  | Windows Defender               |
| ntrtscan.exe | Trend Micro OfficeScan         |
| tmlisten.exe | Trend Micro OfficeScan         |

Search for active AV processes on hosts (local admin priveleges required):

```
Cmd > WMIC /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntiVirusProduct Get displayName
PS > Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntivirusProduct
PS > gc .\100-hosts.txt | % {gwmi -Query "select * from Win32_Process" -ComputerName $_ | ? {$_.Caption -in "avp.exe","cpda.exe","MsMpEng.exe","ntrtscan.exe","tmlisten.exe"} | select ProcessName,PSComputerName}
```

Identify Microsoft.NET version from inspecting assembly properties:

```
PS > cd C:\Windows\Microsoft.NET\Framework64\
PS > ls
PS > cd .\v4.0.30319\
PS > Get-Item .\clr.dll | Fl
Or
PS > [System.Diagnostics.FileVersionInfo]::GetVersionInfo($(Get-Item .\clr.dll)).FileVersion
```

Identify Microsoft.NET version from querying the registry:

```
PS > Get-ItemProperty "HKLM:SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" -Name Release
```

Windows build <-> default .NET Framework version associations:

| Windows Build | Default .NET Framework Version |
| ------------- | ------------------------------ |
| 1511          | 4.6.1                          |
| 1607          | 4.6.2                          |
| 1703          | 4.7                            |
| 1709          | 4.7.1                          |
| 1803          | 4.7.2                          |
| 1909+         | 4.8                            |

.NET Framework version <-> CLR version associations:

| .NET Framework Version | CLR Version |
| ---------------------- | ----------- |
| 2.0, 3.0, 3.5          | 2           |
| 4, 4.5-4.8             | 4           |

Note that we don't have to target the exact .NET Framework version when compiling our tools. It's enough to match the above relationship between .NET Framework version and CLR version, i. e. all 4.x versions will execute on CLR v4. For example, Rubeus compiled to target v4.5 will run on a machine with only .NET v4.0 installed.



