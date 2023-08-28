# \*⃣ Passive

![](../../.gitbook/assets/banner.png)

#### <mark style="color:green;">OSINT (short for Open-Source Intelligence Ghering) is a way of knowing your target without any sorts of direct contact or leaving any evidence of the recon.</mark>

{% hint style="info" %}
In OSINT you should <mark style="color:green;">**always ask questions like: how, who, when, where and why.**</mark> also try to <mark style="color:green;">**collect and sort everything you find and make a structured map**</mark> of the intel you have gathered using a mind mapping tool like [XMind](https://www.xmind.net) or [Mind Master](https://www.mindmaster.io).
{% endhint %}

{% embed url="https://github.com/jivoi/awesome-osint#-geospatial-research-and-mapping-tools" %}

{% embed url="https://s0cm0nkey.gitbook.io/s0cm0nkeys-security-reference-guide/cyber-intelligence/osint" %}

## <mark style="color:red;">The OSINT Process</mark>

#### <mark style="color:green;">OSINT reconnaissance can be further broken down into the following 5 sub-phases:</mark>

![](../../.gitbook/assets/osint-process.png)

<mark style="color:green;">**Source Identification**</mark><mark style="color:green;">:</mark> as the starting point, in this initial phase the attacker identifies potential sources from which information may be gathered from. Sources are internally documented throughout the process in detailed notes to come back to later if necessary.

<mark style="color:green;">**Data Harvesting**</mark><mark style="color:green;">:</mark> in this phase, the attacker collects and harvests information from the selected sources and other sources that are discovered throughout this phase.

<mark style="color:green;">**Data Processing and Integration**</mark><mark style="color:green;">:</mark> during this phase, the attacker processes the harvested information for actionable intelligence by searching for information that may assist in enumeration.

<mark style="color:green;">**Data Analysis**</mark><mark style="color:green;">:</mark> in this phase, the attacker performs data analysis of the processed information using OSINT analysis tools.

<mark style="color:green;">**Results Delivery**</mark><mark style="color:green;">:</mark> in the final phase, OSINT analysis is complete and the findings are presented/reported to other members of the Red Team.

## <mark style="color:red;">Workflow</mark>

### <mark style="color:orange;">Domain</mark>

![](<../../.gitbook/assets/image (275) (1) (1) (1) (1) (1) (1) (1).png>)

### <mark style="color:orange;">Website</mark>

![](../../.gitbook/assets/OSINT-Website.jfif)

### <mark style="color:orange;">Email</mark>

![](<../../.gitbook/assets/osintemail (1).JPG>)

### <mark style="color:orange;">Location</mark>

![](../../.gitbook/assets/OSINT-Location.png)

### <mark style="color:orange;">Username / Real Name</mark>

![](../../.gitbook/assets/OSINT-Real-Name.png)

### <mark style="color:orange;">Phone</mark>

![](../../.gitbook/assets/OSINT-Telephone.png)

## <mark style="color:red;">OSINT Framework</mark>

[the OSINT framework](https://osintframework.com/) is a great collection of OSINT resources that you should definitely check them out.

![](<../../.gitbook/assets/image (182).png>)

## <mark style="color:red;">Maltego</mark>

is an open source intelligence (OSINT) and graphical link analysis tool for gathering and connecting information for investigative tasks. its preinstalled in kali linux. you can download it from [here](https://www.maltego.com)

![](../../.gitbook/assets/maltego.jpg)

## <mark style="color:red;">DNS Harvesting</mark>

{% embed url="https://dnsdumpster.com" %}

{% embed url="https://dnschecker.org/all-dns-records-of-domain.php" %}

## <mark style="color:red;">Email Harvesting</mark>

{% embed url="https://hunter.io" %}

{% embed url="https://www.nymeria.io/blog/how-to-manually-find-email-addresses-for-github-users" %}

{% embed url="https://www.twilio.com/docs/glossary/email-harvesting" %}

## <mark style="color:red;">Google Hacking</mark>

also named Google dorking, is a hacker technique that uses Google Search and other Google applications to find security holes in web applications, finding loots, targeting databases, login pages or even exposed backup files and directories.

### <mark style="color:orange;">**Google Dorks**</mark>

#### there is an expanding database of these search queries maintained by offensive security folks called the [google hacking database (GHDB) ](https://www.exploit-db.com/google-hacking-database). you can use the site search to find dorks for specific types of targets.

{% embed url="https://pentest-tools.com/information-gathering/google-hacking#" %}

### <mark style="color:orange;">Google Dork Collections</mark>

* [https://github.com/BullsEye0/google\_dork\_list](https://github.com/BullsEye0/google\_dork\_list)
* [https://github.com/rootac355/SQL-injection-dorks-list](https://github.com/rootac355/SQL-injection-dorks-list)
* [https://github.com/unexpectedBy/SQLi-Dork-Repository](https://github.com/unexpectedBy/SQLi-Dork-Repository)
* [https://github.com/thomasdesr/Google-dorks](https://github.com/thomasdesr/Google-dorks)
* [https://github.com/arimogi/Google-Dorks](https://github.com/arimogi/Google-Dorks)
* [https://github.com/aleedhillon/7000-Google-Dork-List](https://github.com/aleedhillon/7000-Google-Dork-List)
* Bug Bounty Dorks
  * [https://github.com/sushiwushi/bug-bounty-dorks](https://github.com/sushiwushi/bug-bounty-dorks)
  * [https://github.com/hackingbharat/bug-bounty-dorks-archive/blob/main/bbdorks](https://github.com/hackingbharat/bug-bounty-dorks-archive/blob/main/bbdorks)
  * [https://github.com/Vinod-1122/bug-bounty-dorks/blob/main/Dorks.txt](https://github.com/Vinod-1122/bug-bounty-dorks/blob/main/Dorks.txt)
* Backlinks
  * [https://github.com/alfazzafashion/Backlink-dorks](https://github.com/alfazzafashion/Backlink-dorks)
  * [https://www.techywebtech.com/2021/08/backlink-dorks.html](https://www.techywebtech.com/2021/08/backlink-dorks.html)
  * [https://www.blackhatworld.com/seo/get-backlinks-yourself-1150-dorks-for-forum-hunting.380843/](https://www.blackhatworld.com/seo/get-backlinks-yourself-1150-dorks-for-forum-hunting.380843/)
  * CMS Dorks
    * Wordpress [https://pastebin.com/A9dsmgHQ](https://pastebin.com/A9dsmgHQ)
    * Magento [https://pastebin.com/k75Y2QhF](https://pastebin.com/k75Y2QhF)
    * Joomla [https://pastebin.com/vVQFTzVC](https://pastebin.com/vVQFTzVC)

### <mark style="color:orange;">Automated Dork Tools</mark>

[GoogD0rker](https://github.com/ZephrFish/GoogD0rker/)

```
./googD0rker-txt.py -d example.com
```

[Goohak](https://github.com/1N3/Goohak)

```
./goohak domain.com
```

[Pagado](https://github.com/opsdisk/pagodo)

```
# first run the scrapper to get the dorks and store them
python3 ghdb_scraper.py -j -s

# then run the tool to use gathered dorks
# -d option can be used to target a domain
python3 pagodo.py -d example.com -g dorks.txt -l 50 -s -e 35.0 -j 1.1
```

### <mark style="color:orange;">Advanced Search Keywords</mark>

besides the google dorks which are more advanced, there are some google search tricks (keywords) that will make your life easier. these are the keywords used in advanced google search**e**s:

#### <mark style="color:green;">**cache:**</mark>

If you include other words in the query, Google will highlight those words within the cached document. For instance, \[cache:www.google.com web] will show the cached content with the word “web” highlighted. This functionality is also accessible by clicking on the “Cached” link on Google’s main results page. The query \[cache:] will show the version of the web page that Google has in its cache. For instance, \[cache:www.google.com] will show Google’s cache of the Google homepage. Note there can be no space between the “cache:” and the web page url.

#### <mark style="color:green;">**link:**</mark>

The query \[link:] will list webpages that have links to the specified webpage. For instance, \[link:www.google.com] will list webpages that have links pointing to the Google homepage. Note there can be no space between the “link:” and the web page url.

<mark style="color:green;">**related:**</mark>

The query \[related:] will list web pages that are “similar” to a specified web page. For instance, \[related:www.google.com] will list web pages that are similar to the Google homepage. Note there can be no space between the “related:” and the web page url.

#### <mark style="color:green;">**info:**</mark>

The query \[info:] will present some information that Google has about that web page. For instance, \[info:www.google.com] will show information about the Google homepage. Note there can be no space between the “info:” and the web page url.

#### <mark style="color:green;">**define:**</mark>

The query \[define:] will provide a definition of the words you enter after it, gathered from various online sources. The definition will be for the entire phrase entered (i.e., it will include all the words in the exact order you typed them).

#### <mark style="color:green;">**stocks:**</mark>

If you begin a query with the \[stocks:] operator, Google will treat the rest of the query terms as stock ticker symbols, and will link to a page showing stock information for those symbols. For instance, \[stocks: intc yhoo] will show information about Intel and Yahoo. (Note you must type the ticker symbols, not the company name.)

#### <mark style="color:green;">**site:**</mark>

If you include \[site:] in your query, Google will restrict the results to those websites in the given domain. For instance, \[help site:www.google.com] will find pages about help within www.google.com. \[help site:com] will find pages about help within .com urls. Note there can be no space between the “site:” and the domain.

#### <mark style="color:green;">**allintitle:**</mark>

\*\*\*\* If you start a query with \[allintitle:], Google will restrict the results to those with all of the query words in the title. For instance, \[allintitle: google search] will return only documents that have both “google” and “search” in the title.

#### <mark style="color:green;">**intitle:**</mark>

If you include \[intitle:] in your query, Google will restrict the results to documents containing that word in the title. For instance, \[intitle:google search] will return documents that mention the word “google” in their title, and mention the word “search” anywhere in the document (title or no). Note there can be no space between the “intitle:” and the following word. Putting \[intitle:] in front of every word in your query is equivalent to putting \[allintitle:] at the front of your query: \[intitle:google intitle:search] is the same as \[allintitle: google search].

#### <mark style="color:green;">**inurl:**</mark>

If you include \[inurl:] in your query, Google will restrict the results to documents containing that word in the url. For instance, \[inurl:google search] will return documents that mention the word “google” in their url, and mention the word “search” anywhere in the document (url or no). Note there can be no space between the “inurl:” and the following word. Putting “inurl:” in front of every word in your query is equivalent to putting “allinurl:” at the front of your query: \[inurl:google inurl:search] is the same as \[allinurl: google search].

#### <mark style="color:green;">and these are some simple rules for combining the queries and dorks as well:</mark>

OR : ( | )

AND: (&)

NOT

define : define a word or phrase

## <mark style="color:red;">Github Dorks</mark>

we can use github advanced search keywords and dorks to find sensitive data in repositories.

{% hint style="info" %}
Github dorks work with filenames and extentions
{% endhint %}

```
filename:bashrc
extension:pem
langage:bash
```

some examples of github search keywords:

```
extension:pem private # Private SSH Keys
extension:sql mysql dump # MySQL dumps
extension:sql mysql dump password # MySQL dumps with passwords
filename:wp-config.php # Wordpress config file
filename:.htpasswd # .htpasswd
filename:.git-credentials # Git stored credentials
filename:.bashrc password # .bashrc files containing passwords
filename:.bash_profile aws # AWS keys in .bash_profiles
extension:json mongolab.com # Keys/Credentials for mongolab
HEROKU_API_KEY language:json # Heroku API Keys
filename:filezilla.xml Pass # FTP credentials
filename:recentservers.xml Pass # FTP credentials
filename:config.php dbpasswd # PHP Applications databases credentials
shodan_api_key language:python # Shodan API Keys (try others languages)
filename:logins.json # Firefox saved password collection (key3.db usually in same repo)
filename:settings.py SECRET_KEY # Django secret keys (usually allows for session hijacking, RCE, etc)
```

## <mark style="color:red;">Open Job Requisitions</mark>

#### <mark style="color:green;">Job requisitions can help us get information about the information technology products used in a target organization, such as:</mark>

* Web server type
* Web application dev environment
* Firewall type
* Routers

### <mark style="color:orange;">Google searches to find job reqs</mark>

* site: \[ companydomain ] careers Q , Keyword or Title 9
* site: \[ companydomain ] jobs .
* site: \[ companydomain ] openings
* Also, searches of job-related site

## <mark style="color:red;">PGP Public Key Servers</mark>

Organizations maintain servers that provide public PGP keys to clients. You can query these to reveal user email addresses and details.

{% embed url="https://pgp.mit.edu" %}

{% embed url="https://keyserver.ubuntu.com" %}

{% embed url="https://pgp.uni-mainz.de" %}

## <mark style="color:red;">CloadFlare / Tor IP Detection</mark>

some tips to find real IP addresses hiding behind CloadFlare and Tor

{% embed url="https://www.secjuice.com/finding-real-ips-of-origin-servers-behind-cloudflare-or-tor/" %}

## <mark style="color:red;">Identify Host Sharing</mark>

see if a single server or ip is hosting multiple websites/domains:

```
# Bing dorks to identify host sharing
ip:xxx.xxx.xxx.xxx
```

## <mark style="color:red;">Shodan</mark>

Search engine for the Internet of everything. Shodan is the world's first search engine for Internet-connected devices including computers, servers, CCTV cameras, SCADA systems and everything that is connected to the internet with or without attention. Shodan can be used both as a source for gathering info about random targets for mass attacks and a tool for finding weak spots in a large network of systems to attack and take the low-hanging fruit. Shodan has a free and commercial membership and is accessible at [shodan.io](https://www.shodan.io) . the search syntax in the search engine is somehow special and can be found in the help section of the website. with shodan you can search for specific systems, ports, services, regions and countries or even specific vulnerable versions of a software or OS service running on systems like SMB v1 and much more.

here the keywords that are mostly used in shodan search queries:

### <mark style="color:orange;">Shodan Queries</mark>

{% embed url="https://www.osintme.com/index.php/2021/01/16/ultimate-osint-with-shodan-100-great-shodan-queries" %}

```
title: Search the content scraped from the HTML tag
html: Search the full HTML content of the returned page
product: Search the name of the software or product identified in the banner
net: Search a given netblock (example: 204.51.94.79/18)
version: Search the version of the product
port: Search for a specific port or ports
os: Search for a specific operating system name
country: Search for results in a given country (2-letter code)
city: Search for results in a given city
! : NOT
```

for example these are some queries you can use with these keywords:

```
Also, searches of job-related sites
.ir
- www . monster . com: Search on Info Tech and Internet / E - commerce• Also, searches of job-related sites• Also, searches of job-related siteshostname:megacorpone.com
title:"smb" !port:139,445
product:IIS 8.5
Microsoft-IIS/5.0 title:"outlook web"
net:100.10.23.0/24 unauthorized
html:"eBay Inc. All Rights Reserved"
"Authentication: disabled" port:445
shodan count microsoft iis 6.0
shodan host 189.201.128.250
shodan myip
shodan parse --fields ip_str,port,org --separator , microsoft-data.json.gz 
shodan search --fields ip_str,port,org,hostnames microsoft iis 6.0
```

### <mark style="color:orange;">Shodan CLI & nmap</mark>

there are several other ways to use the search engine without the website for example with the nmap NSE scripts like this:

```
nmap -sn -Pn -n --script=shodan-api -script-args shodan-api.apikey=[api key] [target ip]
```

there is also a CLI shodan interface written in python for linux which you can use or integrate in your own scripts or tools. to install and setup the CLI tool:

```
   pip install shodan
   shodan init <api key>
   shodan -h
```

you can use the CLI tool by simply specifying a single host/IP:

```
shodan host [target ip]
```

shodan will return ports, services and even some possible CVEs ( which are not very reliable ).

#### <mark style="color:green;">for the free API keys you cant use the same method to scan a whole net block but with some bash voodoo you can use the free API instead of paid ones to scan the whole /24 net block this way and see if any systems on this net block is exposed :</mark>

```
for host in {1..254}; do shodan host 192.168.1.$host 2>&1 | grep -v "Error" ; done
```

#### and if you want to aggressively scan a /16 net block you can do this:

```
 for netblock in {1..254};do for host in {1..254}; do shodan host 192.168.$netblock.$host 2>&1 | grep -v "Error" ; done ; done
```

and here are some other useful resources about shodan:

[https://www.sans.org/blog/getting-the-most-out-of-shodan-searches](https://www.sans.org/blog/getting-the-most-out-of-shodan-searches/)\
[https://thor-sec.com/cheatsheet/shodan/shodan\_cheat\_sheet](https://thor-sec.com/cheatsheet/shodan/shodan\_cheat\_sheet/)\
[https://github.com/jakejarvis/awesome-shodan-queries](https://github.com/jakejarvis/awesome-shodan-queries)

## <mark style="color:red;">Credential Leaks</mark>

Tools and resources for credential leaks available online:

{% embed url="https://pastebeen.com/" %}

{% embed url="https://github.com/thewhiteh4t/pwnedOrNot.git" %}

{% embed url="http://pwndb2am4tzkvold.onion" %}

{% embed url="https://dehashed.com" %}

{% embed url="https://haveibeenpwned.com" %}

{% embed url="https://breachdirectory.tk/" %}

## <mark style="color:red;">Social Media Investigation</mark>

#### For more in-depth social search check the [social platforms page](social-platforms/).

## <mark style="color:red;">Other OSINT Websites</mark>

I have put together a list of the most used OSINT sources that will usually cover about 90% of your needs in a regular pentest. remember there are endless ways to find Intel about your target. the OSINT process is limited to your own imagination.

### <mark style="color:orange;">Top sources ( most used )</mark>

* #### [Skip Tracing Framework (kind of all-in-one directory for recon)](https://makensi.es/stf/)
* #### [Robtex (search for IPs, domain names, etc )](https://www.robtex.com)
* #### &#x20;[Netcraft (very useful for website and domain recon)](https://searchdns.netcraft.com)&#x20;
* #### [SSL labs (test websites and domains SSL cert security)](https://www.ssllabs.com/ssltest)&#x20;
* #### [Security Headers (test website headers (browser plugin is available)](https://securityheaders.com)&#x20;
* #### [Archive.org (the largest Internet archive)](https://archive.org)&#x20;
* #### [iseek (not as deep as others but still useful)](https://www.iseek.com)&#x20;
* #### [Global file search (search for any file, used for passive metadata search )](http://globalfilesearch.com)&#x20;
* #### [NSLookup (query DNS records, both web and CLI tool )](https://network-tools.com/nslookup/)&#x20;
* #### [DNSdumpster (great for DNS recon)](https://dnsdumpster.com)&#x20;
* #### [Whois (both web and CLI tool )](https://www.whois.net)&#x20;
* #### [ONYPHE (internet SIEM website, that's what they call themselves )](https://www.onyphe.io)

### <mark style="color:orange;">Image search</mark>

* [**TinEye ( reverse image search )**](https://tineye.com)&#x20;
* #### [photo bucket ( image search )](https://photobucket.com)



### <mark style="color:orange;">Username and people search</mark>

* [**User search ( search for usernames, mostly social media networks )**](https://usersearch.org)&#x20;
* [**Thats them**](https://thatsthem.com)&#x20;
* [**pipi ( investigation and research, you should sign up for it )**](https://pipl.com)&#x20;
* [**Social mention ( social media search )**](http://socialmention.com)&#x20;
* [**Social searcher ( social media search )**](https://www.social-searcher.com)&#x20;
* [**SPOKEO ( name, phone number, address, etc. )**](https://www.spokeo.com)&#x20;
* [**Find people search ( people search )**](http://www.findpeoplesearch.com)&#x20;
* [**Social bearing**](https://www.socialbearing.com) [**TwimeMachine**](https://www.twimemachine.com)

### <mark style="color:orange;">IOT and device search</mark>

* [**shodan ( search engine for internet connected devices, command line )**](https://www.shodan.io)
* &#x20;[**open stream cam ( open stream camera )**](file:///root/work/w4lk3rn3t/recon/osint/index.html)&#x20;
* [**insecam ( live video camera search )**](file:///root/work/w4lk3rn3t/recon/osint/index.html)

####

### <mark style="color:orange;">Dark web engines</mark>

#### pubpeer.com

scholar.google.com

arxiv.org

guides.library.harvard.edu

deepdotweb.com

Core.onion

OnionScan

Tor Scan

ahmia.fi

not evil

### <mark style="color:orange;">Monitoring and alerting</mark>

Google Alerts

HaveIBeenPwned.com

Dehashed.com

Spycloud.com

Ghostproject.fr

weleakinfo.com/

{% embed url="https://GetNotify.com" %}

## <mark style="color:red;">Tools and Frameworks</mark>

#### There are countless number of tools out there designed for active/passive recon. you wont need to know about every single one of them because most of them use the same techniques for gathering these information. in this section i will briefly introduce you to the best/well-known tools that i usually use:

### <mark style="color:orange;">Theharvester</mark>

a well-known tool among pentesters and OSINT investigators which is mostly good for collecting subdomains and email addresses.

{% embed url="https://github.com/laramies/theHarvester" %}

harvester is preinstalled on pentesting OSs like kali and parrot but for others you can install it from github and run it in docker:

```
apt install theharvester
Theharvester --help

theHarvester -d target.com -b google,bing,baidu,bufferoverun,crtsh,dnsdumpster,duckduckgo,github-code,hackertarget,netcraft,rapiddns,rocketreach,sublist3r,trello,urlscan  -n -r -v -s --screenshot target/harvester -g 
```

```
git clone https://github.com/laramies/theHarvester
cd theHarvester
docker build -t theharvester .
docker run theharvester -h 
```

### <mark style="color:orange;">h8mail</mark>

An email OSINT and breach hunting tool using different breach and reconnaissance services, or local breaches such as Troy Hunt's "Collection1" and the infamous "Breach Compilation" torrent.

{% embed url="https://github.com/khast3x/h8mail" %}

```
python3 -m pip install h8mail

h8mail -t target@example.com
```

### <mark style="color:orange;">gitrob</mark>

a tool to help find potentially sensitive files pushed to public repositories on Github. Gitrob will clone repositories belonging to a user or organization down to a configurable depth and iterate through the commit history and flag files that match signatures for potentially sensitive files. The findings will be presented through a web interface for easy browsing and analysis.

{% embed url="https://github.com/michenriksen/gitrob/releases/tag/v2.0.0-beta" %}

### <mark style="color:orange;">inspy</mark>

LinkedIn enumeration tool. preinstalled on kali.

{% embed url="https://github.com/leapsecurity/InSpy" %}

```
inspy --empspy /usr/share/inspy/wordlists/title-list-large.txt --emailformat flast@google.com 'Google'

--email format is how the emails work

inspy --empspy /usr/share/inspy/wordlists/title-list-large.txt --emailformat
```

### <mark style="color:orange;">amass</mark>

In-depth Attack Surface Mapping and Asset Discovery, preinstalled on kali. The OWASP Amass Project performs network mapping of attack surfaces and external asset discovery using open source information gathering and active reconnaissance techniques.

{% embed url="https://github.com/OWASP/Amass" %}

run the help to see the options:

```
amass --help
```

### <mark style="color:orange;">spiderfoot</mark>

an open source intelligence (OSINT) automation tool. It calmes to integrate with just about every data source available and utilities a range of methods for data analysis, making that data easy to navigate.

{% embed url="https://github.com/smicallef/spiderfoot" %}

```
spiderfoot -l 127.0.0.1:5001  → run web GUI on local host, connect with browser
```

### <mark style="color:orange;">recon-ng</mark>

By far, the best recon framework out there with both active and passive modules. designed like metasploit framework and each type of recon has its own specific module and options. the modules are installed from the "marketplace" plus a bunch of reporting modules for different formats. recon-ng is preinstalled on kali linux and parrot OS.

{% embed url="https://github.com/lanmaster53/recon-ng" %}

you can see the full ocumentation in the wiki:

{% embed url="https://github.com/lanmaster53/recon-ng/wiki" %}

here are some of the useful commands for a quick start:

```
help >>> help
marketplace install/search/info [modules names] >>> add or search for a module
modules load [module] >>> load a module
info >>> show module options
options set >>> set module options
run >>> run the module
```

to install all recon modules at once:

```
marketplace install recon/
```

some modules need api keys add it with :

```
e.g: shodan for example keys add shodan_api <API>
show keys — list available API keys
keys add api_key_name #api_key_value →  add key to module
```

a list of modules i usually use:

```
recon/domains-domains/brute_suffix 
recon/domains-hosts/bing_domain_web  
recon/domains-hosts/brute_hosts
recon/domains-hosts/netcraft
recon/domains-hosts/ssl_san
recon/hosts-hosts/bing_ip
recon/domains-hosts/hackertarget
recon/netblocks-hosts/reverse_resolve       → find hosts in a netblock
recon/hosts-hosts/reverse_resolve
discovery/info_disclosure/cache_snoop       → useful for finding AVs in use
```

### <mark style="color:orange;">Sherlock</mark>

find user accounts on social media

{% embed url="https://github.com/sherlock-project/sherlock" %}

```
git clone https://github.com/sherlock-project/sherlock.git

cd sherlock
python3 -m pip install -r requirements.txt
python3 sherlock --help
python3 sherlock user1 user2 user3
```

### <mark style="color:orange;">TWINT</mark>

advanced Twitter scraping tool written in Python that allows for scraping Tweets from Twitter profiles without using Twitter's API.

{% embed url="https://github.com/twintproject/twint" %}

### <mark style="color:orange;">social-analyzer</mark>

for analyzing and finding a person's profile across +800 social media websites

```
python3 -m pip install social-analyzer

social-analyzer --username "johndoe" --metadata --extract --mode fast
```
