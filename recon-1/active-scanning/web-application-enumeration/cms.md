# CMS

## Wordpress

The WordPress version is shown in the "generator" meta tag \(unless removed by the site\). You may search the source code \(CTRL-F\) for "generator" to see the version. This curl command will also show it. The "-s" flag is for "silent"

```text
curl -s http://example.com/wordpress/ | grep generator
```

###  basic information about wordpress

```text
wpscan --url https://192.168.26.141
```

### check for vulnerable plugins

```text
wpscan --url https://192.168.26.141:12380/blogblog --enumerate vp
```

### check for exploits that match the version of wordpress

```text
wpscan --no-update --url http://www.example.com/wordpress/
wpscan --no-update --url http://www.example.com/wordpress/ | grep Title
wpscan --no-update --url http://www.example.com/wordpress/ | grep Title | wc -l
```

### vulnerability and plugin scan

```text
wpscan --url sandbox.local --enumerate ap,at,cb,dbe
```

### enumerate usernames

```text
wpscan --url http://192.168.56.149/wordpress/ --enumerate u --force --wp-content-dir wp-content
```

### password attack on discovered usernames

```text
wpscan --url http://192.168.56.149/wordpress/ --passwords /usr/share/wordlists/fasttrack.txt --usernames userlist -t 25
```

### enumerate everything

```text
wpscan --url https://192.168.26.141
```

### scan with nmap NSE scripts

```text
nmap -sV --script http-wordpress-enum 10.11.1.234
nmap -Pn --script http-wordpress-enum --script-args check-latest=true,search-limit=10 10.11.1.234
nmap -sV 10.11.1.234 --script http-wordpress-enum --script-args limit=25
```

## Drupal

### droopscan

installation:

```text
apt-get install python-pip
pip install droopescan
```

scanning:

```text
droopescan scan drupal -u example.org        
droopescan scan -u example.org
droopescan scan -U list_of_urls.txt
```

## Joomla

### joomscan

```text
joomscan --url http://192.168.56.126 -ec
```

## Nikto

A free web application vulnerability scanner preinstalled on kali linux.

```text
nikto -host example.com
```

