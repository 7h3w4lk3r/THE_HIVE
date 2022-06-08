# ⭕ RDP Downgrade

RDP MiTM attacks are possible through the usage of a tool called Seth. Seth is a tool written in Python and Bash to MitM RDP connections by attempting to downgrade the connection in order to extract clear text credentials. It was developed to raise awareness and educate about the importance of properly configured RDP connections in the context of pentests, workshops or talks. The author is Adrian Vollmer (SySS GmbH).

{% hint style="info" %}
This attack works on latest versions on windows with default configurations.
{% endhint %}

## <mark style="color:red;">SETH</mark>

{% embed url="https://github.com/SySS-Research/Seth" %}

{% embed url="https://www.cyberpunk.rs/seth-rdp-mitm-attack-tool" %}

#### run:

```
sudo ./seth.sh <interface> <Attacker-PC> <Victim-PC> <Server>
sudo ./seth.sh eth1 10.10.10.6 10.10.10.7 10.10.10.10 
```

![](<../../.gitbook/assets/image (10) (1) (1).png>)

Then on the Victim-PC the victim initiates an RDP session as part of a routine task to the Domain Controller on 10.10.10.10.

On connection request we see the following.

![](<../../.gitbook/assets/image (3) (1).png>)

Where normally a normal RDP connection will produce a results similar to that of below. Taking note of the extra warnings provided above as opposed to the legitimate request below.

![](<../../.gitbook/assets/image (22) (1) (1) (1) (1).png>)

Assuming the victim accepts the malicious certificate request the Attacking-PC will receive a Network NTLM hash for the user and the users password in cleartext.

![](<../../.gitbook/assets/image (2) (1) (1).png>)

The victim user should continue to connect to the destination server and be none the wiser to the attack.

if the group policy option  "Disallow connections if the certificate cannot be validated" is enabled, we cant see the cleartext credentials, however, we will get the user hash anyways and we can pass or crack that hash later.

![](<../../.gitbook/assets/image (14) (1) (1) (1).png>)
