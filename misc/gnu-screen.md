# 🔴 GNU Screen

{% embed url="https://www.shellhacks.com/linux-screen-command-run-in-background/" %}

{% embed url="https://blog.ronin.cloud/gnuscreen/" %}

#### <mark style="color:green;">GNU Screen creates screen in background run command from shell or script</mark>

```bash
apt install screen
```

### <mark style="color:orange;">Usage</mark>

**In your terminal, execute the **<mark style="color:green;">**`screen`**</mark>** command and **<mark style="color:green;">**press space or enter key**</mark>** to close the intro window. you should see no difference after doing so.**

**then **<mark style="color:green;">**run your command**</mark>** in that open screen session and send it to background with **<mark style="color:green;">**`Ctrl+Z`**</mark>** .**&#x20;

**then type in **<mark style="color:green;">**`bg`**</mark>** to send the screen session itself to the background as well.**

**after this you will be back to screen and by typing **<mark style="color:green;">**`screen -d`**</mark>** the screen session will be sent to your default shell background in that terminal.**

#### <mark style="color:green;">now the application is running in background and terminating the terminal or the SSH session will not terminate the process.</mark>

If you want to launch and connect to screen:

```bash
screen CMD
```

If you want to launch and not connect to screen:

```bash
screen -dm CMD
```

Works with sessions too:

```bash
screen -Sdm NewDetachedSessionName CMD
```

You can send keypresses to `CMD` with `stuff`:

```bash
screen -S NewDetachedSessionName -X stuff "keypresses"
```

{% hint style="info" %}
To send a new-line, include  or `^M` or `$'\n'` with the _keypresses_.
{% endhint %}

irst create new session :

`screen -dmS [session_name]`

then attach command or script to run in session created:

`screen -x [session_name] [script.sh]`
