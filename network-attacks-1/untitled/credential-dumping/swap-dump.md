# Swap Dump

As everything is a “file” in linux, so is swap space, and we can use that to our advantage using built-in tools.

One caveat to this technique is that this has to be done as the root account, and may also be prone to false-positives as it’s difficult to ascertain exactly where in swap memory sensitive information will be temporarily stored.

The partition or “file” defined as the swap file can be found with the following commands:

```
swapon -s
```

In the output from the above command, we can see that our swap partition is at /dev/sda5.

We can obtain the exact same information by issuing the “cat” command to the “/proc/swaps” file:

```
cat /proc/swaps
```

The process from here is straightforward. We can use the strings command against the /dev/sda5 partition (in this case) while grep’ing for strings we’re looking for. Here are a couple of examples:

```
strings /dev/sda5 |grep “password=“
strings /dev/sda5 |grep “&password=“
```

A shell script “swap\_digger.sh” has also been written which can automate searching for common sensitive strings within the swap file, and be downloaded at the following link:

{% embed url="https://github.com/sevagas/swap_digger" %}

```
git clone https://github.com/sevagas/swap_digger.git
./swap_digger.sh
```
