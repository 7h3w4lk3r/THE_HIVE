# DC - 1

nmap enumeration

![](../.gitbook/assets/1111111111111111%20%282%29.png)

web app enumeration 

![](../.gitbook/assets/1111111111111111%20%2813%29.png)

we use droopscan to enumerate the CMS

![](../.gitbook/assets/1111111111111111%20%281%29.png)

we see a list of possible drupal versions, with a quick google search we find out that metasploit has some exploits for 7.2x versions:

![](../.gitbook/assets/1111111111111111%20%2816%29.png)

we use drupageddon  and open a shell in meterpreter:

![](../.gitbook/assets/1111111111111111%20%289%29.png)

we start the post exploitation enumeration and search for SUID/SGID binaries that are exploitable:

```text
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null
```

we see a few SUID binaries and by searching the [https://gtfobins.github.io/](https://gtfobins.github.io/) database we see that find command is vulnerable to privilege escalation, the instruction is in the website:

![](../.gitbook/assets/1111111111111111%20%2818%29.png)

and we have a root shell :\)







