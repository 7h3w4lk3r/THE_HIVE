# Kernel Parameters

## <mark style="color:red;">Namespace Isolation</mark>

Namespaces are a kernel security feature that was introduced in Linux kernel version 2.4.19, all the way back in 2002. A namespace allows a process to have its own set of computer resources that other processes can't see. They're especially handy for times when you might have multiple customers sharing resources on the same server. The processes for each user will have their own namespaces. Currently, there are seven types of namespaces:

<mark style="color:orange;">Mount (mnt):</mark> This is the original namespace, which was introduced in Linux kernel 2.4.19. At the time, this was the only namespace. This allows each process to have its own root filesystem that no other processes can see, unless you choose to share it. This is a good way of preventing information leakage.

<mark style="color:orange;">UTS:</mark> The UTS namespace allows each process to have its own unique hostname and domain name.

<mark style="color:orange;">PID:</mark> Every running process can have its own set of PID numbers. PID namespaces can be nested so that a parent namespace can see the PIDs of child namespaces. (Note that child namespaces can't see into the parent namespaces.)

<mark style="color:orange;">Network (net):</mark> This allows you to create a whole virtual network for each process. Each virtual network can have its own subnets, virtual network interfaces, routing tables, and firewalls.

<mark style="color:orange;">Interprocess Communication (ipc):</mark> This also prevents data leakage by preventing two processes from sharing the same memory space. Each running process can access its own memory space, but other processes will be blocked.

<mark style="color:orange;">Control group (cgroup):</mark> This namespace hides the identity of the cgroup that a process is a member of.

<mark style="color:orange;">User :</mark> The User namespace allows a user to have different levels of privilege on different processes. For example, a user could have root-level privileges on one process, but only normal-user privileges on another process.

To see these namespaces, just go into any numbered directory within the /proc filesystem and view the contents of the ns directory.

![](<../../../.gitbook/assets/image (279).png>)

you will see that there's an extra item in this directory that we haven't covered. The `pid_for_children` item tracks PIDs in child namespaces.

## <mark style="color:red;">Control Groups (cgroups)</mark>

Control Groups, more commonly called cgroups, were introduced back in 2010 in Red Hat Enterprise Linux 6. Originally, they were just an add-on feature, and a user had to jump through some hoops to manually create them. Nowadays, with the advent of the systemd init system, cgroups are an integral part of the operating system, and each process runs in its own cgroup by default.

<mark style="color:orange;">With cgroups, processes run in their own kernel space and memory space. Should the need arise, an administrator can easily configure a cgroup to limit the resources that the process can use.</mark> This is not only good for security, but also for tuning system performance.

it's really just a collection of processes that are grouped together for a particular purpose. Here's what you can do with cgroups:

*   <mark style="color:orange;">Set resource limits :</mark> For each cgroup, you can set resource limits for CPU usage, I/O usage, and memory usage. Perform different accounting functions: You can measure resource usage for each cgroup, which makes it easy to bill specific customers for the resources that they use.


*   <mark style="color:orange;">Prioritize resources :</mark> You can set limits on a user who's hogging resources like crazy.


* <mark style="color:orange;">Freezing, checkpointing, and restarting :</mark> These functions are handy for troubleshooting. They allow you to stop a process, take a snapshot of the system state, and restore a system state from a backup.

### <mark style="color:red;">place resource limits on processes</mark>

By default, each cgroup on the system has no defined resource limits. The first step in defining them is to enable accounting for CPU usage, memory usage, and I/O usage. We could do that by hand-editing the systemd service file for each service that we want to limit, but it's easier to just run a systemctl command, like so:

```
sudo systemctl set-property httpd.service MemoryAccounting=1
CPUAccounting=1 BlockIOAccounting=1
```

The command would be the same on an Ubuntu machine, except that we would have apache2.service instead of httpd.service

when we look in the `/etc/systemd/system.control` directory, we'll see that we've created an httpd.service.d directory. Within that directory are the files that turn on our accounting functions:

![](<../../../.gitbook/assets/image (300).png>)

Inside each file, we can see two lines that modify the original httpd.service file in order to turn on accounting. For example, here's the one for CPUAccounting :&#x20;

![](<../../../.gitbook/assets/image (272).png>)

Now that we've enabled accounting for the Apache service, we can place some resource limits on it. (By default, there are no limits.) Let's say that we want to limit Apache to only 40% of CPU usage and 500 MB of memory usage. We'll set both limits with the following command:

```
sudo systemctl set-property httpd.service CPUQuota=40% MemoryLimit=500M
```

This command created two more files in the `/etc/systemd/system.control/httpd.service.d/` directory:

![](<../../../.gitbook/assets/image (290).png>)

![](<../../../.gitbook/assets/image (294) (1).png>)

We can allocate resources to other services in the same manner. For example, if this were a Linux-Apache-MySQL/MariaDB-PHP (LAMP) server, we could allocate a portion of the remaining CPU and memory resources to the PHP service, and the rest to the MySQL/MariaDB service.

### <mark style="color:red;">place resource limits on user accounts</mark>

enable accounting for a user and set resource limits:

```
sudo systemctl set-property user-1001.slice MemoryAccounting=1 CPUAccounting=1 BlockIOAccounting=1

sudo systemctl set-property user-1001.slice CPUQuota=20% MemoryLimit=500M
```

If we look in the `/etc/systemd/system.control/user-1001.slice.d` directory, we'll see the same set of files that were created for the httpd service.

## <mark style="color:red;">Kernel Capabilities</mark>

When you perform a ps aux command — or a sudo ps aux command if you've mounted /proc with the hidepid=1 or hidepid=2 option — you'll see many processes that are owned by the root user. This is because these processes have to access some sort of system resource that unprivileged users can't access. However, having services run with full root privileges can be a bit of a security problem. Fortunately, there are some ways to mitigate that.

Capabilities allow the Linux kernel to divide what the root user can do into distinct units. Let's say that you've just written a cool custom program that needs to access a privileged network port. Without capabilities, you'd either have to start that program with root privileges and let it run with root privileges or jump through the hoops of programming it so that it can drop root privileges once it's been started

There are too many capabilities to list here (there's about 40 in all), but you can see the full list by using the following command:

```
man capabilities
```

to get the capabilities of a program:

```
getcap /usr/bin/python2.7
```

to set the caps (check them in man page and choose what's best for your needs ):

```
sudo setcap 'CAP_NET_BIND_SERVICE+ep'  /usr/bin/python2.7
```

## <mark style="color:red;">SECCOMP & Containers</mark>

Secure Computing (SECCOMP), originally created for the Google Chrome web browser, **allows you to either enable just a certain subset of syscalls that you want for a process to use or disable certain syscalls that you want to prevent a process from using**. Unless you're a software developer or a Docker container developer, you probably won't be working with this directly all that much. However, this is yet another building block for the technologies that are used daily by normal humans.

{% embed url="https://kubernetes.io/docs/tutorials/clusters/seccomp" %}

{% embed url="https://docs.docker.com/engine/security/seccomp" %}

{% embed url="https://training.play-with-docker.com/security-seccomp" %}

{% embed url="https://dzone.com/articles/hardening-docker-container-using-seccomp-security" %}
