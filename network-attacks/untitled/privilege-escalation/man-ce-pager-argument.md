# man CE Pager Argument

The man program usually utilizes the more or less programs to display its pages depending on its configuration. The program it uses for this purpose is known as the “ pager, ” and can be specified with the “ -P ” switch when running man . If we look at the man page for man , we can see what the pager option is about:

```
man man
```

Due to a quirk in the man program and how it handles the pager (-P) argument, we can run a command that we want; for example, we’ll tell man to run the “id” command to display our UID and GID information:

```
man -P "id" man
```

![](<../../../.gitbook/assets/image (12).png>)

```
sudo man -P "cat /etc/shadow" man
```
