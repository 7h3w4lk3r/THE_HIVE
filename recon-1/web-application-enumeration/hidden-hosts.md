# ⭕ Hidden Hosts

## <mark style="color:red;">Finding Hidden Virtual Hosts/Subdomains</mark>

certificate transparency reports offer a penetration tester an additional method for discovering hosts and subdomains within a domain. this includes hidden virtual hosts that are not linked publicly and google doesn't know about them but they have been issued an x.509 cert. use these links for cert discovery:

{% embed url="https://crt.sh/" %}

{% embed url="https://transparencyreport.google.com/https/certificates" %}

#### here is a simple bash script i wrote to automate the subdomain enumeration task. it uses various online resources to discover every subdomain that is publicly available. <a href="#here-is-a-simple-bash-script-i-wrote-to-automate-the-subdomain-enumeration-task-it-uses-various-onli" id="here-is-a-simple-bash-script-i-wrote-to-automate-the-subdomain-enumeration-task-it-uses-various-onli"></a>

```
#!/bin/bash

# simple script to find subdomains from various online sources

if [ $# -ne 1 ]
then
	echo "usage: $0 <target>"
	exit 1
fi

TARGET=$1


printf "\n [>] Target set to : ${TARGET} [<]\n"

printf "\n[+] Subdomain discovery started, this might take some time depending on your net speed [+]\n"

printf "\n [*] Searching rapiddns.io [*]\n"

(curl -s "https://rapiddns.io/subdomain/$TARGET?full=1#result" 2>/dev/null | grep "<td><a" 2>/dev/null | cut -d '"' -f 2  2>/dev/null | grep http 2>/dev/null | cut -d '/' -f3 2>/dev/null | sed 's/#results//g' 2>/dev/null | sort -u 2>/dev/null) > output.txt

printf "\n [*] Searching  bufferover.run [*]\n"

(curl -s https://dns.bufferover.run/dns?q=.$TARGET 2>/dev/null |jq -r .FDNS_A[] 2>/dev/null |cut -d',' -f2 2>/dev/null|sort -u 2>/dev/null ) >> output.txt

printf "\n [*] Searching riddler.io [*]\n"

(curl -s "https://riddler.io/search/exportcsv?q=pld:${TARGET}" 2>/dev/null| grep -Po "(([\w.-]*)\.([\w]*)\.([A-z]))\w+" 2>/dev/null| sort -u 2>/dev/null ) >> output.txt

printf "\n [*] Searching www.virustotal.com [*]\n"

(curl -s "https://www.virustotal.com/ui/domains/${TARGET}/subdomains?limit=40" 2>/dev/null | grep -Po "((http|https):\/\/)?(([\w.-]*)\.([\w]*)\.([A-z]))\w+" 2>/dev/null | sort -u 2>/dev/null ) >> output.txt

printf "\n [*] Searching certspotter.com [*]\n"

(curl -s "https://certspotter.com/api/v1/issuances?domain=${TARGET}&include_subdomains=true&expand=dns_names"  2>/dev/null | jq .[].dns_names 2>/dev/null | tr -d '[]"\n ' 2>/dev/null | tr ',' '\n'2>/dev/null  ) >> output.txt

printf "\n [*] Searching jldc.me [*]\n"

(curl -s "https://jldc.me/anubis/subdomains/${TARGET}"  2>/dev/null | grep -Po "((http|https):\/\/)?(([\w.-]*)\.([\w]*)\.([A-z]))\w+"  2>/dev/null | sort -u  2>/dev/null   ) >> output.txt


printf "\n [*] Searching sonar.omnisint.io [*]\n"

(curl --silent https://sonar.omnisint.io/subdomains/$TARGET 2>/dev/null | grep -oE "[a-zA-Z0-9._-]+\.$TARGET" 2>/dev/null | sort -u 2>/dev/null ) >> output.txt

printf "\n [*] Searching synapsint.com [*]\n"

(curl --silent -X POST https://synapsint.com/report.php -d "name=https%3A%2F%2F$TARGET" 2>/dev/null| grep -oE "[a-zA-Z0-9._-]+\.$TARGET" 2>/dev/null | sort -u 2>/dev/null ) >> output.txt

printf "\n [*] Searching crt.sh [*]\n"

(curl -s "https://crt.sh/?q=%25.$TARGET&output=json" 2>/dev/null| jq -r '.[].name_value' 2>/dev/null| sed 's/\*\.//g' 2>/dev/null| sort -u 2>/dev/null ) >> output.txt


printf "\n [*] Sorting the output [*]\n"

cat output.txt > temp.txt
cat temp.txt | sort -u > output.txt
rm -rf temp.txt 
mv output.txt $TARGET.txt

printf "\n [+] Output saved to $TARGET.txt [+]\n"
```
