# Sandboxing

Sandboxing involves providing a safe environment for a program or software so that you can play around with it without hurting your system. It actually keeps your program isolated from the rest of the system, by using any one of the different methods available in the Linux kernel.

### <mark style="color:orange;">Namespaces</mark>

Namespaces are features available in Linux to isolate processes in different system resource aspects. There are six types of namespaces available up to kernel 4.0. And more will be added in the future. These are:

* _mnt_ (mount points, file systems)
* _pid_ (processes)
* _net_ (network stack)
* _ipc_ (system V IPC)
* _uts_ (host name)
* _user_ (UIDs)

Creation of new namespaces is done by the ``` `_`clone()`_` ``` system call, which is also used to start a process. The `setns()` system call adds a running process to the existing namespace. The `unshare()` call works on a process inside the namespace, and makes the caller a member of the namespace. Its main purpose is to isolate the namespace without having to create a new process or thread (as is done by ``` `_`clone()`_).You can directly use some services to get the features of these namespaces. CLONE\_NEW\* identifiers are used with these system calls to identify the type of namespace. These three system calls make use of the `CLONE_NEW*` as `CLONE_NEWIPC`, `CLONE_NEWNS`, `CLONE_NEWNET`, `CLONE_NEWPID`, `CLONE_NEWUSER`, and `CLONE_NEWUTS`. A process in a namespace can be different because of its unique inode number when it is created.

```
#ls -al /proc/<pid>/ns
lrwxrwxrwx 1 root root 0 Feb 7 13:52 ipc -> ipc:[4026532253]
lrwxrwxrwx 1 root root 0 Feb 7 15:39 mnt -> mnt:[4026532251]
lrwxrwxrwx 1 root root 0 Feb 7 13:52 net -> net:[4026531957]
lrwxrwxrwx 1 root root 0 Feb 7 13:52 pid -> pid:[4026532254]
lrwxrwxrwx 1 root root 0 Feb 7 13:52 user -> user:[4026531837]
lrwxrwxrwx 1 root root 0 Feb 7 15:39 uts -> uts:[4026532252]
```

### <mark style="color:orange;">Mount namespace</mark>

A process views different mount points other than the original system mount point. It creates a separate file system tree associated with different processes, which restricts them from making changes to the root file system.

### <mark style="color:orange;">PID namespace</mark>

PID namespace isolates a process ID from the main PID hierarchy. A process inside a PID namespace can have the same PID as a process outside it, and even inside the namespace, you can have different init with PID 1.

### <mark style="color:orange;">UTS namespace</mark>

In the UTS (UNIX Timesharing System) namespace, a process can have a different set of domain names and host names than the main system. It uses sethostname() and setdomainname() to do that.

### <mark style="color:orange;">IPC namespace</mark>

This is used for inter-process communication resources isolation and POSIX message queues.

### <mark style="color:orange;">User namespace</mark>

This isolates user and group IDs inside a namespace, which is allowed to have the same UID or GID in the namespace as in the host machine. In your system, unprivileged processes can create user namespaces in which they have full privileges.

### <mark style="color:orange;">Network namespace</mark>

Inside this namespace, processes can have different network stacks, i.e., different network devices, IP addresses, routing tables, etc.

Sandboxing tools available in Linux use this namespaces feature to isolate a process or create a new virtual environment. A much more secure tool will be that which uses maximum namespaces for isolation. Now, lets talk about different methods of sandboxing, from soft to hard isolation.

## <mark style="color:red;">chroot</mark>

_chroot_ is the oldest sandboxing tool available in Linux. Its work is the same as mount namespace, but it is implemented much earlier. _chroot_ changes the root directory for a process to any _chroot_ directory (like /_chroot_). As the root directory is the top of the file system hierarchy, applications are unable to access directories higher up than the root directory, and so are isolated from the rest of the system. This prevents applications inside the _chroot_ from interfering with files elsewhere on your computer. To create an isolated environment in old SystemV based operating systems, you first need to copy all required packages and libraries to that directory. For demonstration purposes, I am running  is on the chroot directory.

First, create a directory to set as root a file system for a process:

```
<em>#mkdir /chroot</em>
```

Next, make the required directory inside it.

```
<em>#mkdir /chroot/{lib,lib64,bin,etc}</em>
```

Now, the most important step is to copy the executable and libraries. To get the shell inside the _chroot_, you also need _/bin/bash_





