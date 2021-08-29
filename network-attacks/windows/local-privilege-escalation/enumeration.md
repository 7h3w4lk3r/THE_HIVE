# Enumeration

## System Information

### general system information

```text
systeminfo
```

for OS name and version \( can add any other filter too \) :

```text
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
```

show information for a remote system \( used in domains \) :

```text
systeminfo /S [ip or hostname]
```

### SNMP configurations

check to see if there is any SNMP services running and find valid community strings:

```text
reg query HKLM\SYSTEM\CurrentControlSet\Services\SNMP /s 
```

{% hint style="info" %}
for more information on windows registry hive and 'reg' commands refer to this section of the book.
{% endhint %}



















