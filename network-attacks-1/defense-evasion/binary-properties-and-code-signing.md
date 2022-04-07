# ⭕ Binary Properties & Code Signing

## <mark style="color:red;">Binary Properties</mark>

Most of your legitimate binaries have some sort of properties attached to them. As we can see with ntdll.dll, we have many properties of this binary which make it seem more legitimate:

![](<../../.gitbook/assets/image (21).png>)

But with a freshly compiled binary, we see this:

![](<../../.gitbook/assets/image (45).png>)

We see a lot of empty fields which can be flagged as suspicious behaviour. Making our binary have these fields filled out can make our binary look more legit and pass under the radar more. To fill out the empty fields, we can use resource hacker for this. To do this click on the file tab and open our executable that we want to fill up the empty field with:

![](<../../.gitbook/assets/image (47) (1).png>)

Then, import the binary you want to clone the module details from by clicking this and then selecting the binary you want to clone:

![](<../../.gitbook/assets/image (23).png>)

![](<../../.gitbook/assets/image (20).png>)

Then check off the version info box:

![](<../../.gitbook/assets/image (19) (1).png>)

You should now see something like this, you can then modify the descriptions and values to fit whatever you are doing but for now, we are just going to stick with this:

![](<../../.gitbook/assets/image (33) (1).png>)

After, we can then click the save button to save our file.

Let's then check our the properties of our modified PE file:

![](<../../.gitbook/assets/image (22).png>)

We have now filled up the binary properties of our executable

## <mark style="color:red;">Code Signing</mark>

{% embed url="https://stackoverflow.com/questions/84847/how-do-i-create-a-self-signed-certificate-for-code-signing-on-windows" %}

{% embed url="https://blog.jayway.com/2014/09/03/creating-self-signed-certificates-with-makecert-exe-for-development" %}

&#x20;The next issue I want to discuss is code signing. Some AV engines may flag unsigned binaries as suspicious due to the fact that most legitimate binaries in windows ten have some code signing already. We can see our PE file does not have a code signing tab in the its properties:

![](<../../.gitbook/assets/image (11).png>)

But binaries like ntdll.dll have one:

![](<../../.gitbook/assets/image (5).png>)

To sign a binary, we will need a certificate authority and code-signing certificate.&#x20;

Let's first make the self signed CA:

```
makecert -r -pe -n "CN=Malwr CA" -ss CA -sr CurrentUser -a sha256 -cy authority -sky signature -sv MalwrCA.pvk MalwrCA.cer
```

Then make the self signed Cert:

```
makecert -pe -n "CN=Malwr Cert" -a sha256 -cy end -sky signature -ic MalwrCA.cer -iv MalwrCA.pvk -sv MalwrCert.pvk MalwrCert.cer
```

Convert it to PFX:

```
pvk2pfx -pvk MalwrCert.pvk -spc MalwrCert.cer -pfx MalwrCert.pfx
```

and then sign our binary with it:

```
signtool sign /v /f MalwrCert.pfx /t http://timestamp.verisign.com/scripts/timstamp.dll <executable>
```
