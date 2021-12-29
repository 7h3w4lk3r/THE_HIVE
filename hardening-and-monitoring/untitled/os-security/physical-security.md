# Physical Security

## <mark style="color:red;">Disabling Ctrl+Alt+Del (Systemd)</mark>

```
systemctl mask ctrl-alt-del.target
systemctl daemon-reload
```
