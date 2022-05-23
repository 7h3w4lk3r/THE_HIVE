# ⭕ Directories & Subdomains

## <mark style="color:red;">Directory and Subdomain Discovery</mark>

### <mark style="color:orange;">sublist3r</mark>

```
sublist3r -d [domain] -t [threads] -o [output] -v -b [brute force mode>]
```

### <mark style="color:orange;">OWASP OpenDoor</mark>

```
python3 opendoor.py --host 10.10.10.6 --scan=directories -t 50
```

### <mark style="color:orange;">Cansina</mark>

```
python ./cansina.py -u 10.10.10.6 -p ./directories.dat --persist -t 50 --show-type --full-path -b 403,404
```

### <mark style="color:orange;">DIRB</mark>

```
dirb http://example.com -r
```

### <mark style="color:orange;">dirsearch</mark>

one of the best tools for discovering sub-directories and metadata search.

{% embed url="https://github.com/maurosoria/dirsearch" %}

```
python3 dirsearch.py -u http://192.168.56.103 -e php,exe.elf.cgi,asp,txt,pdf,png,jpg -r --suppress-empty -t 5 --clean-view -w db/dicc.txt -x 403,404

python3 tools/webapp/dirsearch/dirsearch.py -e php,exe.elf.cgi,asp,txt,pdf,png,jpg -r --suppress-empty -t 5 --clean-view -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -x 403,404 -u example.com -l output/subdomains --full-url --random-agents --follow-redirects --simple-report=subdir
```

### <mark style="color:orange;">patator</mark>

```
patator http_fuzz url=[url] method=POST body
```

### <mark style="color:orange;">gobuster</mark>

Used for both subdomain/vhost and subdirectory discovery.

```
gobuster dir -w /usr/share/wordlists/dirb/directory-list-2.3-medium.txt -u 10.10.10
```

some useful options:

```
-x → extention
dir → directory brutforce
dns → subdomain bruteforce
-c → cookie string
-e → print full url
-U → username for auth
-p → password for basic auth
-P → proxy [http(s)://host:port]
-s → set positive status codes will be overwritten with statuscodesblacklist if set) (default "200,204,301,302,307,401,403")
-u → url
-o → output to a file
-k → skip ssl
-a → set user agent
```

#### <mark style="color:green;">GoBuster Web Content Discovery</mark>

```
gobuster -u http:/// -w /usr/share/seclists/Discovery/ Web_Content/common.txt -s '200,204,301,302,307,403,500' -e
```

#### <mark style="color:green;">GoBuster subdomain brute forcing</mark>

```
gobuster dns -w /usr/share/seclists/Discovery/ DNS/subdomains-top1million-110000.txt -d target.com
```

#### <mark style="color:green;">GoBuster subdomain vhost search</mark>

```
gobuster vhost -u example.com -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt 
```

### <mark style="color:orange;">dirbuster</mark>

add url wtih port : example: [http://127.0.0.1:80/](http://127.0.0.1)

wordlist for bruteforce is in /usr/share/wordlists/dirbuster

set go faster 200 threats (usually works best) in file extentions add any file type you want to look for like rar,docx,zip, etc.

![](<../../.gitbook/assets/image (155).png>)

### <mark style="color:orange;">wfuzz</mark>

#### it can fuzz any given location in a url, the location is specified by the "FUZZ" parameter:

```
wfuzz -u http://sneakycorp.htb -w /usr/share/wordlists/dirbuster/directory-list -2.3-small.txt -H 'Host: FUZZ.sneakycorp.htb' --hw 1
```

#### find files and extensions, Hide 404 codes:

```
wfuzz -u http://10.10.15.205/firstdirc/seconddir/FUZZ.extension -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -c --hc 404
```
