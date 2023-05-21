# Samba Secrets to Domain Admin

One interesting method we can use to move from one Linux machine to other systems in an organization involves dumping machine account passwords stored on a Samba server that we discover has a trust relationship with an Active Directory infrastructure.

When a new Samba user is created, this information is usually stored in what is known as the `secrets.tdb` file.

In Samba version 4.7.4 on Debian, the `secrets.tdb` file is stored in the `/var/lib/samba/private` directory.

Assuming we’re root on a machine, we can use the `tdbdump` command to dump the information in the `secrets.tdb` file with the following command:

```
tdbdump /var/lib/samba/private/secrets.tdb
```

The output of which would be something similar to the following:

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

In the output on the previous slide, we can see that the machine is a member of the foocorp.com domain, and can also see that one of the data fields contains encoded data that decodes to the NTLM hash of the computer account for this machine, which we’ll see in the next slides.

Now assuming the Samba server has a valid trust relationship with an Active Directory domain, we can decode the results of the tdbdump UTF8 encoded “data” fields to obtain the NTLM hash for the Samba computer account and ultimately pass-the-hash to Active Directory using pth-smbclient from the pth-toolkit.

This method is described in great detail at the following link which shows us how we can move from the Samba server to Active Directory as a computer account, to ultimately obtaining Domain Admin-level privileges:

{% embed url="https://medium.com/@br4nsh/from-linux-to-ad-10efb529fae9" %}
