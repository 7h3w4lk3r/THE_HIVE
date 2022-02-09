# Vino VNC Server

GNOME ships a VNC Server which can be activated with vino-preferences. Or for the lazy people:

```
gconftool-2 --set /desktop/gnome/remote_access/enabled --type bool true
gconftool-2 --set /desktop/gnome/remote_access/prompt_enabled --type bool false
gconftool-2 --set /desktop/gnome/remote_access/view_only --type bool false
```

