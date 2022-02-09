# Xinetd UDP portnock

thia utilizes the built-in “xinetd” daemon, which is used to manage network-based services on Linux systems. Xinetd listens for incoming requests to ports we can define, and when a specific request is received, we can have it execute a command of our choosing. Of course, this is useful to us!

For our purposes, we will create a custom xinetd service, which listens on a UDP port which we can send a single UDP packet to. Once that packet is received to the port we define, it will initiate a netcat reverse shell back to our attacker system. We’re calling it a UDP port-knock backdoor because once we “knock” on our UDP port, it gives us an immediate reverse shell

Port knocking is a well-known concept, but we’re implementing it differently, not requiring any third-party software.

{% embed url="https://en.wikipedia.org/wiki/Port_knocking" %}

Another bonus is it will persist across reboots

To create our xinetd backdoor, we should follow these steps:

Step 1. On the target machine, we’re going to create a custom xinetd service. We’ve put together a bash script (xinetd\_server.sh) you can simply run on the target host, and it can be downloaded here:

{% embed url="https://gist.github.com/anonymous/3cb8e474b6bb3fd3787bda1e1a55cf56" %}

![](<../../../.gitbook/assets/1 (23).png>)

The following script, when executed, will create a new xinetd service called “services-udp.” The “services-udp” custom service will be configured to listen on port 65534 UDP on the target machine. It will also require that netcat (nc) is on the target host in the usual /bin/ directory. It will copy the nc executable to a file called “/bin/services-udp.” We’re obscuring netcat as another file in this case.

Step 2. On the attacker machine, simply start up a netcat listener with the same  you defined in the xinetd\_server.sh script before running it. In the below example, we’re using 4444:

```
nc -nlvp 4444
```

step 3. For the final step, we will use a tool called hping3 to send a single UDP packet to port 65534 on the target host, at which point, our custom “services-udp” netcat binary will be triggered, and send us a reverse shell. The hping command we will use is:

```
hping3 -2 -c 1 <target IP> -p 65534
```

![](<../../../.gitbook/assets/1 (24).png>)













