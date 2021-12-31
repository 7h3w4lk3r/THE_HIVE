# Object Management

The <mark style="color:orange;">Active Directory Users and Computers (ADUC)</mark> <mark style="color:orange;">Microsoft Management Console (MMC)</mark>  can manage AD objects. With AD DS 2008 R2, Microsoft introduced the <mark style="color:orange;">Active Directory Administrative Center (ADAC),</mark> which is a built-in PowerShell command-line interface technology

To access the ADAC console, you can type `dsac.exe` in a PowerShell command line or the Run box.

## <mark style="color:red;">Creating user objects</mark>

we can use the New-ADUser PowerShell cmdlet. You can view the full syntax of the command along with the accepted data types using the following command:

```
Get-Command New-ADUser -Syntax
```

In order to create a new user account using PowerShell, the minimum value you need to pass is -Name . It will create a disabled user account, and you can define values for other attributes later.

```
New-ADUser -Name "Talib Idris" -GivenName "Talib" -Surname "Idris" -SamAccountName "tidris" -UserPrincipalName "tidris@rebeladm
```

Instead of executing multiple commands to create multiple user objects, we can create a Comma-Separated Values (CSV) file that includes data for attributes and use it to create accounts in one go.

![](<../../../.gitbook/assets/image (281).png>)

```
Import-Csv "C:\ADUsers.csv" | ForEach-Object {
$upn = $_.SamAccountName + "@rebeladmin.com"
New-ADUser -Name $_.Name `
-GivenName $_."GivenName" `
-Surname $_."Surname"
-SamAccountName $_."samAccountName" `
-UserPrincipalName $upn `
-Path $_."Path" `
-AccountPassword (ConvertTo-SecureString "Pa$$w0rd" -AsPlainText -force) -Enabled $true
}
```

## <mark style="color:red;">Creating computer objects</mark>

When a desktop computer or member server is joined to a domain, it will create a computer object in AD. This computer object can be created before being added to the domain. This will not add the device to the domain, but it can be used with offline domain joins and RODC domain joins.

```
Get-Command New-ADComputer -Syntax
```

The minimum attribute you need to create a computer object is -Name:

```
New-ADComputer -Name "REBEL-PC-01" -SamAccountName "REBEL-PC-01" -Path "OU=Computers,OU=Europe,DC=rebeladmin,DC=com"
```

## <mark style="color:red;">Modifying AD objects</mark>

We can use the Set-ADUser cmdlet to change and add attribute values to existing AD user objects or change the old ones:

```
Set-ADUser tidris -OfficePhone "0912291120" -City "London"
```

Computer object values can also be added/changed using a similar method. In order to do so, we need to use the Set-ADComputer cmdlet:

```
Set-ADComputer REBEL-PC-01 -Description "Sales Computer"
```

This cmdlet can also be combined with a search query using the Get-ADComputer cmdlet:

```
Get-ADComputer -Filter {Name -like "REBEL-PC-*"} | Set-ADComputer -Location "M35 Building"
```

## <mark style="color:red;">Removing AD objects</mark>

we can use the Remove-ADUser cmdlet

```
Get-Command Remove-ADUser -Syntax
Remove-ADUser -Identity "dzhang"
```

This cmdlet can also be combined with the search query to find objects before removing them:

```
Get-ADUser -Filter {Name -like "Test1*"} | Remove-ADUser
```

In the preceding command, we search for the user whose name starts with Test1 in the entire directory and then remove it.

The Remove-ADComputer cmdlet can be used to remove computer objects from the directory:

```
Remove-ADComputer -Identity "REBEL-PC-01"
Get-ADComputer -Filter * -SearchBase 'OU=Computers,OU=Europe,DC=rebeladmin,DC=com' | Remove-ADComputer
```

In the preceding command, we search for the computer objects in the given OU and then remove the findings from the directory.

## <mark style="color:red;">Finding objects in AD</mark>

```
Get-ADUser -Identity user1 -Properties *
```

The preceding command will list all of the attributes and values associated with user1 . This helps us to find the exact attribute names and common values, which can be used for further filtering.

I need to know the values for Name , UserPrincipalName , and Modified for all of the users. The following command will create a table with relevant attributes and their values:

```
Get-ADUser -Filter * -Properties Name,UserPrincipalName,Modified | ft Name,UserPrincipalName,Modified
```

I can see some accounts in the list that are service and administrator accounts. I only want to see the accounts in the Kingston office:

```
Get-ADUser -Filter {City -like "Kingston"} -Properties Name,UserPrincipalName,Modified | ft Name,UserPrincipalName,Modified
```

Now, I have the list of data I need, and I'd like to export it to a CSV file for future use, like so:

```
Get-ADUser -Filter {City -like "Kingston"} -Properties Name,UserPrincipalName,Modified | select-object Name,UserPrincipalName,Modified
```

### <mark style="color:orange;">search based on the account and password status</mark>

```
Get-Command Search-ADAccount -Syntax
```

it can be used to filter accounts that are locked out:

```
Search-ADAccount -LockedOut | FT Name,UserPrincipalName
```

## <mark style="color:red;">MSAs</mark>

A service account is a dedicated account with specific privileges that is used to run services, batch jobs, and management tasks. In most infrastructures, service accounts are typical user accounts with the Password never expire option

Microsoft's MSAs have the following:

* There is no more password management. MSAs use a complex and random, 240-character password that changes automatically when it reaches the domain or computer password expiry date.&#x20;
* An MSA cannot be locked out or used for interactive login. Only one MSA can be used in one computer. It cannot be shared between multiple computers.&#x20;
* An MSA provides simplified SPN management; the system will automatically change the SPN value if the SamAccountName details of the computer change or DNS name property changes.

In order to create an MSA, we can use the following command. I am running this from the domain controller:

```
New-ADServiceAccount -Name "MyAcc1" -RestrictToSingleComputer
```

we have created a service account called MyAcc1 and restricted it to one computer.

The next step is to associate the service account with the host REBEL- SRV01 server, where I am going to use this service account:

```
Add-ADComputerServiceAccount -Identity REBEL-SRV01 -ServiceAccount "MyAcc1"
```

The next step is to install the service account in the REBEL-SRV01 server. We need the AD PowerShell module for this. We can install it using Remote Server Administration Tools (RSAT). This can be done by running the Install-WindowsFeature RSAT-AD-Tools command. Once it's ready, run the following command:

```
Install-ADServiceAccount -Identity "MyAcc1"
```

We can test the service account using the following command:

```
Test-ADServiceAccount "MyAcc1"
```

It returns `True` , which means the test was successful.

From the AD server, we can verify the service account by running the following command:

```
Get-ADServiceAccount "MyAcc1"
```

## <mark style="color:red;">gMSAs</mark>

There are operational requirements that require the same service account to be shared in multiple hosts. Microsoft's Network Load Balancing (NLB) feature and Internet Information Services (IIS) server farms are good examples of this. All the hosts in these server groups are required to use the same service principal for authentication. gMSAs provide the same functionalities as MSAs, but they extend the higher AD forest level.

The gMSA has the following capabilities:

* No password management&#x20;
* Supports sharing across multiple hosts&#x20;
* Can be used to run scheduled tasks (MSAs do not support the running of scheduled tasks)&#x20;
* Uses Microsoft's Key Distribution Center (KDC) to create and manage passwords for the gMSA

<mark style="color:orange;">Key Distribution Service (KDS)</mark> was introduced with Windows Server 2012 . KDS shares a secret (root key ID) among all the KDS instances in the domain. This value changes periodically. When a gMSA requires a password, a Windows Server domain controller generates a password based on a common algorithm that includes a root key ID. Then, all the hosts that share the gMSA will query from the domain controllers to retrieve the latest password.

In order to start the configuration process, we need to create a KDS root key. This needs to be run from the domain controller with Domain Admin or Enterprise Admin privileges:

```
Add-KdsRootKey –EffectiveImmediately
```

Once this is executed, there is a default 10-hour time limit to replicate the root key to all the domain controllers and start processing gMSA requests. In a testing environment with one domain controller, you can forcibly remove this waiting time and start responding to gMSA requests immediately. This is not recommended for a production environment. We can remove the 10-hour replication time limit by using the following command:

```
Add-KdsRootKey –EffectiveTime ((get-date).addhours(-10))
```

After that, we can create the first gMSA account. I have created an AD group, IISFARM , and have added all my IIS servers to it. This farm will be using the new gMSA:

```
New-ADServiceAccount "Mygmsa1" -DNSHostName "web.rebeladmin.com" –PrincipalsAllowedToRetrieveManagedPassword "IISFARM"
```

we can verify the new account using the following command:

```
Get-ADServiceAccount "Mygmsa1"
```

The next step is to install Mygmsa1 on the server in the IIS farm. Mygmsa1 needs the AD PowerShell module to run. Mygmsa1 can be installed using RSAT:

```
Install-ADServiceAccount -Identity "Mygmsa1"
```

Once that's executed, we can test the service account by running the following command:

```
Test-ADServiceAccount " Mygmsa1"
```

#### Similar to the case with MSAs, when you configure a gMSA with any service, leave the password blank.

### <mark style="color:orange;">Uninstalling MSAs</mark>

```
Remove-ADServiceAccount –identity "Mygmsa1"
```





