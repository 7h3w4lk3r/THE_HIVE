# 🔧 Sandboxing

#### Sandboxing involves providing a safe environment for a program or software so that you can play around with it without hurting your system. It actually keeps your program isolated from the rest of the system, by using any one of the different methods available in the Linux kernel.

## <mark style="color:red;">chroot</mark>

_chroot_ command in Linux/Unix system is used to change the root directory. Every process/command in Linux/Unix like systems has a current working directory called **root directory**. It changes the root directory for currently running processes as well as its child processes.\
A process/command that runs in such a modified environment cannot access files outside the root directory. This modified environment is known as “chroot jail” or **“jailed directory”**. Some root user and privileged process are allowed to use chroot command.

### <mark style="color:orange;">systemv based</mark>

First, create a directory to set as root a file system for a process:

```
<em>#mkdir /chroot</em>
```

Next, make the required directory inside it.

```
<em>#mkdir /chroot/{lib,lib64,bin,etc}</em>
```

Now, the most important step is to copy the executable and libraries. To get the shell inside the _chroot_, you also need _/bin/bash_

```
cp -v /bin/{bash,ls} /chroot/bin
```

To see the libraries required for this script, run the following command:

```
#ldd /bin/bash
linux-vdso.so.1 (0x00007fff70deb000)
libncurses.so.5 => /lib/x86_64-linux-gnu/libncurses.so.5 (0x00007f25e33a9000)
libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5 (0x00007f25e317f000)
libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f25e2f7a000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f25e2bd6000)
/lib64/ld-linux-x86-64.so.2 (0x00007f25e360d000)
 
#ldd /bin/ls
linux-vdso.so.1 (0x00007fff4f8e6000)
libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f9f00aec000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f9f00748000)
libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f9f004d7000)
libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f9f002d3000)
/lib64/ld-linux-x86-64.so.2 (0x00007f9f00d4f000)
libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f9f000b6000)
```

Now, copy these files to the _lib_ or _lib64_ of _/chroot_ as required.

Once you have copied all the necessary files, its time to enter the _chroot_.

```
sudo chroot /chroot/ /bin/bash
```

You will be prompted with a shell running inside your virtual environment. Here, you dont have much to run besides ls, but it has changed the root file system for this process to _/chroot._

To get a more full-featured environment you can use the _debootstrap_ utility to bootstrap a basic Debian system:

```
debootstrap --arch=amd64 unstable my_deb/
```

It will download a minimal system to run under _chroot_. You can use this to even test 32-bit applications on 64-bit systems or for testing your program before installation. To get process management, mount _proc_ to the _chroot_, and to make the contents of _home_ lost on exit, mount _tmpfs_ at _/home//_:

```
sudo mount -o bind /proc my_deb/proc
mount -t tmpfs -o size=100m tmpfs /home/user
```

To get Internet connection inside, use the following command:

```
sudo cp /etc/resolv.conf /var/chroot/etc/resolv.conf
```

After that, you are ready to enter your environment.

```
chroot my_deb/ /bin/bash
```

Here, you get a whole basic operating system inside your _chroot_. But it differs from your main system by mount point, because it only uses the mount property as the isolator. It has the same hostname, IP address and process running as in the main system. That’s why it is much less secure (this is even mentioned in the man page of chroot), and any running process can still harm your computer by killing your tasks or affecting network based services.

{% hint style="info" %}
&#x20;__ To run graphical applications inside chroot, open x server by running the following command on the main system:

`xhost +`

and on chroot system

`export DISPLAY=:0.0`
{% endhint %}

### <mark style="color:orange;">systemd based</mark>

On _systemd_ based systems, chrooting is pretty straightforward. Its needed to define the root directory on the processes unit file only.

```
[Unit]
Description=my_chroot_Service
[Service]
RootDirectory=/chroot/foobar
ExecStartPre=/usr/local/bin/pre.sh
ExecStart=/bin/my_program
RootDirectoryStartOnly=yes
```

Here _RootDirectory_ shows where the root directory is for the foobar process.

{% hint style="info" %}
The program script path has to be inside chroot, which makes the full path of that process script as `/chroot/bin/my_program`.
{% endhint %}

Before the daemon is started, a shell script pre.sh is invoked, the purpose of which is to set up the chroot environment as necessary, i.e., mount _/proc_ and similar file systems into it, depending on what the service might need. You can start your service by using the following command:

```
systemctl start my_chroot_Service.service
```

## <mark style="color:red;">chroot Jail</mark>

The basic command to create a chroot jail is as follows:

```
chroot /path/to/new/root command
#OR
chroot /path/to/new/root /path/to/server
#OR
chroot [options] /path/to/new/root /path/to/server
```

{% hint style="info" %}
_Only a root/privileged user can use the chroot system call. A non-privileged user with the access to the command can bypass the chroot jail._
{% endhint %}

### <mark style="color:orange;">**create a mini-jail for the ‘bash’ and the ‘ls’ command**</mark>

**Create a directory which will act as the root of the command.**

```
mkdir jailed
cd jailed
```

**Create all the essential directories for the command to run**

Depending on your operating system, the required directories may change. Logically, we create all these directories to keep a copy of required libraries. To see what all directories are required, see Step 4.

```
mkdir -p bin lib64/x86_64-linux-gnu lib/x86_64-linux-gnu
```

**Run the ‘which’ command**

Run the ‘which’ command to find the location of ls and bash command.After running which command,copy those binaries in the ‘bin’ directory of our jail. Make sure you don’t have any of these commands aliased. From now on, we would be referring to our directory as **‘Jailed’** directory for convenience.

```
  $ unalias ls          # Required only if you have aliased ls command
  $ unalias bash        # Required only if you have aliased bash command
  $ cp $(which ls) ./bin/
  $ cp $(which bash) ./bin/
```

&#x20;**Copy appropriate libraries/objects**

For the executables in our **Jailed**directory to work we need to copy the appropriate libraries/objects in the JAILED directory. By default, the executable looks at the locations starting with ‘/’. To find the dependencies we use the command ‘ldd’

```
$ ldd $(which bash)
    linux-vdso.so.1 =>  (0x00007ffc75dd4000)
    libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5 (0x00007f6577768000)
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f6577564000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f657719a000)
    /lib64/ld-linux-x86-64.so.2 (0x000055979f3fd000)
```

Run the following commands to create appropriate directories.

```
$ cp /lib/x86_64-linux-gnu/libtinfo.so.5 lib/x86_64-linux-gnu/
$ cp /lib/x86_64-linux-gnu/libdl.so.2 lib/x86_64-linux-gnu/
$ cp /lib/x86_64-linux-gnu/libc.so.6 lib/x86_64-linux-gnu/
$ cp /lib64/ld-linux-x86-64.so.2 lib64/
```

Similarly for ls,

```
$ ldd $(which ls)
    linux-vdso.so.1 =>  (0x00007fff4f05d000)
    libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f9a2fd07000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f9a2f93e000)
    libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f9a2f6cd000)
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f9a2f4c9000)
    /lib64/ld-linux-x86-64.so.2 (0x000055e836c69000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f9a2f2ac000)
```

```
$ cp /lib/x86_64-linux-gnu/libselinux.so.1 lib/x86_64-linux-gnu/
$ cp /lib/x86_64-linux-gnu/libc.so.6 lib/x86_64-linux-gnu/
$ cp /lib/x86_64-linux-gnu/libpcre.so.3 lib/x86_64-linux-gnu/
$ cp /lib/x86_64-linux-gnu/libdl.so.2 lib/x86_64-linux-gnu/
$ cp /lib64/ld-linux-x86-64.so.2  lib64/
$ cp /lib/x86_64-linux-gnu/libpthread.so.0 lib/x86_64-linux-gnu/
```

**Sudo chroot:**Run this command to change the root to the JAILED directory, along with the path to the shell. By default it will try to load ‘/bin/sh’ shell.

```
cd ..
sudo chroot jailed /bin/bash
```

You might face this error while running the chroot command**:**

```
chroot: failed to run command `/bin/bash': No such file or directory
```

This may be due to 2 reasons, either the file does not exist(which is obvious), or when the loading library fails or is not available. Double-Check if the libraries are in correct location.

**A new shell must pop up:**Its our jailed bash. We currently have only 2 commands installed, bash and ls. Fortunately cd and pwd are builtin commands in bash shell, and so you can use them as well.

Roam around the directory, try accessing ‘cd /../’ or something similar. Try to break the jail, probably you won’t be able to.&#x20;

To exit from the jail**:**

```
exit
```

The most important and interesting part is that, when you run:

```
 ps aux
```

and find the process, you’ll find that there is only one process:

```
root     24958  …  03:21   0:00 /usr/bin/sudo -E chroot jailed/ /bin/bash
```

Interestingly, processes in the jailed shell run as a simple child process of this shell. All the processes inside the JAILED environment, are just simple user level process in the host OS and are isolated by the namespaces provided by the kernel, thus there is minimal overhead and as an added benefit we get isolation.

### <mark style="color:orange;">sample script</mark>

{% embed url="https://github.com/pmenhart/make_chroot_jail" %}

{% embed url="https://github.com/cheshirekow/uchroot" %}

{% embed url="https://github.com/kazuho/jailing" %}

## <mark style="color:red;">Ip-netns</mark>

The Ip-netns utility is one of the few that directly use network namespaces to create virtual interfaces. To create a new network namespace, use the following command:

```
ip netns add netns1
```

To check the interfaces inside, use the command shown below:

```
ip netns exec netns ip addr
```

You can even get the shell inside it, as follows:

```
ip netns exec netns /bin/bash
```

This will take you inside the network namespace, which has only a single network interface with no IP. So, you are not connected with the external network and also cant ping.

```
ip netns exec netns ip link set dev lo up
```

This will bring the loop interface up. But to connect to the external network you need to create a virtual Ethernet and add it to _netns_ as follows:

```
# ip link add veth0 type veth peer name veth1
# ip link set veth1 netns netns1
```

Now, its time to set the IP to these devices, as follows:

```
# ip netns exec netns1 ifconfig veth1 10.1.1.1/24 up
# ifconfig veth0 10.1.1.2/24 up
```

## <mark style="color:red;">Unshare</mark>

The unshare utility is used to create any namespace isolated environment and run a program or shell inside it.

To get a network namespace and run the shell inside it, use the command shown below:

```
unshare --net /bin/bash
```

The shell you get back will come with a different network stack. You can check this by using _#ip addr_, as follows:

```
// Some code1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
```

To create a user namespace environment, use the following command:

```
unshare --user /bin/bash
```

You can check your user inside the shell by using the command below:

```
# whoami
nobody
```

To get the PID namespace, use the following command:

```
unshare --pid --fork /bin/bash
```

Inside this namespace, you can see all the processes but cannot kill any.

```
#ps -aux |grep firefox

root 1110 42.6 11.0 1209424 436756 tty1 Sl 23:36 0:15 .firefox1/./firefox
root 1208 0.0 0.0 12660 1648 pts/2 S+ 23:37 0:00 grep firefox
#kill 1110
bash: kill: (1110) - No such process
```

To get a whole different degree of process tree isolation you need to mount another proc for the namespace, as follows:

```
unshare --pid --fork --mount-proc /bin/bash
```

In this way, you can use unshare to create a single namespace. More about it can be found out on the man page of unshare.

A namespace created by using unshare can also be combined to create a single shell which uses different namespaces. For example:

```
#unshare --pid --fork --user /bin/bash
```

This will create an isolated environment using the PID and user namespaces.

## <mark style="color:red;">Firejail</mark>

Firejail is an SUID sandbox program that is used to isolate programs for testing or security purposes. It is written in C and can be configured to use most of the namespaces. To start a service in firejail, use the following command:

```
#firejail firefox
```

It will start Firefox in a sandbox with the root file system mounted as read only. To start Firefox with only _\~/Downloads_ and _\~/.mozilla_ mounted to write, use the following command:

```
firejail --whitelist=~/.mozilla --whitelist=~/Download firefox
```

Firejail, by default, uses the user namespace and mounts empty temporary file systems (_tmpfs_) on top of the user home directory in private mode. To start a program in private mode, use the command given below:

```
firejail --private firefox
```

To start firejail in a new network stack, use the following command:

```
firejail --net=eth0 --whitelist=~/.mozilla --whitelist=~/Download firefox
```

To assign an IP address to the sandbox, use the following command:

```
firejail --net=eth0 --ip=192.168.1.155 firefox
```

To sandbox all programs running by a user, you can change the default shell of that user to /usr/bin/firejail.

```
chsh shell /usr/bin/firejail
```
