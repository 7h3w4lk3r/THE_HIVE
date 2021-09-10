# Installed Applications

Most privilege escalations relating to installed applications are based on misconfigurations we have already covered. Still, some privilege escalations results from things like buffer overflows, so knowing how to identify installed applications and known vulnerabilities is still important.

Manually enumerate all running programs:

```text
 tasklist /v
```

We can also use Seatbelt to search for nonstandard processes:

```text
seatbelt.exe NonstandardProcesses
```

winPEAS also has this ability \(note the misspelling\):

```text
winPEASany.exe quiet procesinfo
```

Once you find an interesting process, try to identify its version. You can try running the executable with /? or -h, as well as checking config or text files in the Program Files directory. Use Exploit-DB to search for a corresponding exploit. Some exploits contain instructions, while others are code that you will need to compile and run.



