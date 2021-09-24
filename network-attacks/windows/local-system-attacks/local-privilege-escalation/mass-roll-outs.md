# Mass Roll-outs

If there is an environment where many machines need to be installed, typically, a technician will not go around from machine to machine. There are a couple of solutions to install machines automatically. What these methods are and how they work is less important for our purposes but the main thing is that they leave behind configuration files which are used for the installation process. These configuration files contain a lot of sensitive sensitive information such as the operating system product key and Administrator password. What we are most interested in is the Admin password as we can use that to elevate our privileges. Typically these are the directories that contain the configuration files \(however it is a good idea to check the entire OS\):

```text
c:\sysprep.inf
c:\sysprep\sysprep.xml
%WINDIR%\Panther\Unattend\Unattended.xml
%WINDIR%\Panther\Unattended.xml
```

#### These files either contain clear-text passwords or in a Base64 encoded format. You can see some sample file output below:

```text
# This is a sample from sysprep.inf with clear-text credentials.
[GuiUnattended]
OEMSkipRegional=1
OemSkipWelcome=1
AdminPassword=s3cr3tp4ssw0rd
TimeZone=20
# This is a sample from sysprep.xml with Base64 "encoded" credentials. Please people Base64 is not
encryption, I take more precautions to protect my coffee. The password here is "SuperSecurePassword".
<LocalAccounts>
    <LocalAccount wcm:action="add">
        <Password>
            <Value>U3VwZXJTZWN1cmVQYXNzd29yZA==</Value>
            <PlainText>false</PlainText>
        </Password>
        <Description>Local Administrator</Description>
        <DisplayName>Administrator</DisplayName>
        <Group>Administrators</Group>
        <Name>Administrator</Name>
    </LocalAccount>
</LocalAccounts>
# Sample from Unattended.xml with the same "secure" Base64 encoding.
<AutoLogon>
    <Password>
        <Value>U3VwZXJTZWN1cmVQYXNzd29yZA==</Value>
        <PlainText>false</PlainText>
    </Password>
    <Enabled>true</Enabled>
    <Username>Administrator</Username>
</AutoLogon>
```



