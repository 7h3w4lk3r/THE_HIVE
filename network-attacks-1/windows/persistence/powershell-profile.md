# Powershell Profile

#### PowerShell profile is a PowerShell script which enables system administrators and users to customize their environment and to execute specific commands when a PowerShell session initiates. It is similar to logon scripts that are used heavily by Administrators to map network drives and printers for users or gather information about the system.

&#x20;Modification of the contents of a PowerShell profile script allows an adversary or a red team to use this as a persistence mechanism if the user performs work on PowerShell on a regular basis. This technique can be executed under the context of the current user.

{% hint style="warning" %}
This technique works only if the user or another program is using powershell to do something.

If the user is not using profiles, the technique will stick out immediately due to the "loading personal and system profiles..." message at the top.
{% endhint %}

There are four places you can abuse the powershell profile, depending on the privileges you have:

```
$PROFILE | select *
```

![](<../../../.gitbook/assets/image (52).png>)

add the code to a `$profile` variable (that expands to the current user's profile file) that will get executed the next time the compromised user launches a powershell console:

```
dir %HOMEPATH%"\Documents\windowspowershell
echo c:\rto\pers\implant\implant.exe > %HOMEPATH%"\Documents\windowspowershell\profile.ps1
```

Once the compromised user launches powershell, our code gets executed.

## <mark style="color:red;">Mitigation</mark>

* Enforce execution of only signed PowerShell scripts. Sign profiles to avoid them from being modified.
* Making PowerShell profiles immutable and only changeable by certain administrators will limit the ability for adversaries to easily create user level persistence.
* Avoid PowerShell profiles if not needed. Use the -No Profile flag with when executing PowerShell scripts remotely to prevent local profiles and scripts from being executed.

## <mark style="color:red;">Detection</mark>

* Locations where `profile.ps1` can be stored should be monitored for new profiles. [\[4\]](http://www.malwarearchaeology.com/s/Windows-PowerShell-Logging-Cheat-Sheet-ver-June-2016-v2.pdf) Example profile locations include: _ `$PsHome\Profile.ps1`_ `$PsHome\Microsoft.{HostProgram}_profile.ps1` _ `$Home\My Documents\PowerShell\Profile.ps1`_ `$Home\My Documents\PowerShell\Microsoft.{HostProgram}_profile.ps1`
* Locations where `profile.ps1` can be stored should be monitored for modifications. [\[4\]](http://www.malwarearchaeology.com/s/Windows-PowerShell-Logging-Cheat-Sheet-ver-June-2016-v2.pdf) Example profile locations include: _ `$PsHome\Profile.ps1`_ `$PsHome\Microsoft.{HostProgram}_profile.ps1` _ `$Home\My Documents\PowerShell\Profile.ps1`_ `$Home\My Documents\PowerShell\Microsoft.{HostProgram}_profile.ps1`

