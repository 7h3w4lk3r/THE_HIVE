# ⭕ CMS

## <mark style="color:red;">Wordpress</mark>

The WordPress version is shown in the "generator" meta tag (unless removed by the site). You may search the source code (CTRL-F) for "generator" to see the version. This curl command will also show it. The "-s" flag is for "silent"

```
curl -s http://example.com/wordpress/ | grep generator
```

### <mark style="color:orange;">basic information</mark>

```
wpscan --url https://192.168.26.141
```

### <mark style="color:orange;">check for vulnerable plugins</mark>

```
wpscan --url https://192.168.26.141:12380/blogblog --enumerate vp
```

### <mark style="color:orange;">check for exploits that match the version of wordpress</mark>

```
wpscan --no-update --url http://www.example.com/wordpress/
wpscan --no-update --url http://www.example.com/wordpress/ | grep Title
wpscan --no-update --url http://www.example.com/wordpress/ | grep Title | wc -l
```

### <mark style="color:orange;">vulnerability and plugin scan</mark>

```
wpscan --url sandbox.local --enumerate ap,at,cb,dbe
```

### <mark style="color:orange;">enumerate usernames</mark>

```
wpscan --url http://192.168.56.149/wordpress/ --enumerate u --force --wp-content-dir wp-content
```

### <mark style="color:orange;">password attack on discovered usernames</mark>

```
wpscan --url http://192.168.56.149/wordpress/ --passwords /usr/share/wordlists/fasttrack.txt --usernames userlist -t 25
```

### <mark style="color:orange;">enumerate everything</mark>

```
wpscan --url https://192.168.26.141
```

### <mark style="color:orange;">scan with nmap NSE scripts</mark>

```
nmap -sV --script http-wordpress-enum 10.11.1.234
nmap -Pn --script http-wordpress-enum --script-args check-latest=true,search-limit=10 10.11.1.234
nmap -sV 10.11.1.234 --script http-wordpress-enum --script-args limit=25
```

## <mark style="color:red;">Drupal</mark>

### <mark style="color:orange;">droopscan</mark>

installation:

```
apt-get install python-pip
pip install droopescan
```

scanning:

```
droopescan scan drupal -u example.org        
droopescan scan -u example.org
droopescan scan -U list_of_urls.txt
```

## <mark style="color:red;">Joomla</mark>

### <mark style="color:orange;">joomscan</mark>

```
joomscan --url http://192.168.56.126 -ec
```

## <mark style="color:red;">Nikto</mark>

A free web application vulnerability scanner preinstalled on kali linux.

```
nikto -host example.com
```
