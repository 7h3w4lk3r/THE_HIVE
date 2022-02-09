# Software Restriction Policies (SRPs)

Group Policy Objects (GPOs) They provide incredibly granular controls over thousands of Windows settings and can be enforced on machines, domains, or any organizational units (OUs). These Administrative Templates increase in number and power as each OS and Service Pack is released.

Software Restriction Policies (SRPs) are defined under the Security Settings section within the Group Policy Editor. The purpose of an SRP is to control the applications that can be run on the system. This can help the administrator to prevent end users from executing binaries downloaded from the internet or transferred by using a portable medium such as a USB stick

#### Two security levels are available:

• Unrestricted: Software access rights are determined by the access rights of the user (default in Windows 2003 or lower)

 • Disallowed: Software will not run, regardless of the access rights of the user

#### Four types of rules currently exist:

**Certificate:** Software publisher certificate has been used to digitally sign the file. 

**Hash:** A cryptographic fingerprint of the file is used.

 **Zone:** From which IE zone the file was downloaded. Path: Location where the file is stored.

**Example:** A system is configured with the default security level Unrestricted. We create a new Hash rule for the mspaint.exe application (located in the C:\Windows directory to "Disallowed." A fingerprint of the mspaint.exe application is stored for later usage. When the end user now tries to start up the Microsoft Paint utility, the Windows OS evaluates the default security level and all the SRP rules and notices that the fingerprint of the executed application matches one of the rules that is set on Disallowed. Execution of the application fails regardless of the permission of the end user on this application. (MS Paint is normally executable by anyone.) See http://technet.microsoft.com/en-us/library/cc507878.aspx for more SRP information.























