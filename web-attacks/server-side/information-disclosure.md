# ⭕ Information Disclosure

When a website unintentionally reveals sensitive information

## <mark style="color:red;">Test for information disclosure</mark>

* Fuzzing
* Using Burp's engagement tools, Search/Find comments/Discover content
* Engineering informative responses

## <mark style="color:red;">Common sources of information disclosure</mark>

<mark style="color:blue;">**Files for Web Crawlers**</mark> Check `/robots.txt` or `/sitemap.xml`

<mark style="color:blue;">**Directory Listings**</mark> Web servers can be configured to automatically list the contents of directories that do not have an index page present

<mark style="color:blue;">**Developer Comments**</mark> Interesting things on in-line HTML/JavaScript comments

<mark style="color:blue;">**Error Messages**</mark> Reveal information in errors

<mark style="color:blue;">**Debugging Data**</mark> Interesting thins in custom error messages for developer debugs

<mark style="color:blue;">**User Account Pages**</mark> Like IDOR vulnerabilities, attacker can access to other user information

<mark style="color:blue;">**Backup Files**</mark> Sensitive data is sometimes even hard-coded within the source code in the backup files

<mark style="color:blue;">**Insecure Configuration**</mark> Test HTTP "**TRACE**" method because sometimes "/admin" path will be enable if you send a special header like: `X-Custom-IP-Authorization: 127.0.0.1`

<mark style="color:blue;">**Version Control History**</mark> Check ".git" folder and find sensitive data hard-coded within some of the changed lines. Useful command: \
&#x20;     `$ git status` \
&#x20;     `$ git log` \
&#x20;     `$ git checkout`
