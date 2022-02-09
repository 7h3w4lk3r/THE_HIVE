# VIM Config Backdoor

## Normal User

{% hint style="info" %}
Obviously this will work only if the user runs the vim editor.
{% endhint %}

The VIM editor is a widely used command line text editor on Linux and it (or its predecessor vi ) is installed on nearly all Unix and Linux systems by default. It is well known for its extensive functionality and, as a result, presents us with an opportunity for exploitation

On many Linux systems, user-specific VIM configuration settings are located in a user’s home directory in the .vimrc file. This file takes VIM-specific scripting commands and configures the VIM environment when a user starts the application.

These commands can also be run from within the editor by typing a colon (:) character followed by the desired command. For example, if we want to print a message to the user, we can use the following command in the .vimrc file or within the editor

```
:echo "this is a test"
```

{% hint style="warning" %}
Putting our commands directly into the user’s `.vimrc` file isn’t particularly stealthy, as a user modifying their own settings may accidentally discover the changes we’ve made. There is, however, another option.

We can “source” a shell script using the bash source command. This loads a specified shell script and runs it for us during the normal configuration process.
{% endhint %}

We can also “import” other VIM configuration files into the user’s current config with the `:source` command. Note that the source call for loading a VIM configuration file is prepended with a colon and not an exclamation point, which is used for shell commands.

As a more stealthy approach, we can leverage the VIM plugin directory. As long as the files have a .vim extension, all VIM config files located in the user’s `~/.vim/plugin` directory will be loaded when VIM is run.

We can modify the user’s .vimrc file in their home directory (or create one if they don’t have it) and add the following line.

```
!source ~/.vimrunscript
```

This will load and run a shell script called `.vimrunscript` from the user’s home directory. In a real- world scenario, it might be useful to pick a file path outside the user’s home directory but for simplicity, we’ll keep it here.

Next, we can create the shell script file at `/home/w4lk3r/.vimrunscript` with the following contents.

```
#!/bin/bash
echo "hacked" > /tmp/hacksrcout.txt
```

The script echoes the word “hacked” to a file called `/tmp/hacksrcout.txt`

if we try to run VIM now, we get an obvious debug output message explaining that we’re sourcing a configuration file.

```
vi /tmp/test.txt
:!source /home/offsec/.vimrunscript
```

This is obviously undesirable as it would tip off the user. Luckily, VIM has a built-in command for this, the `:silent` command.

This command mutes any debug output which would normally be sent to the user when running VIM. We’ll change our line in the user’s .vimrc file to the following.

```
:silent !source ~/.vimrunscript
```

## Sudoer User

In most cases, users with sudo rights are required to enter their password when performing activities with elevated permissions via the sudo command. We can’t perform activities as root via sudo because we don’t know the user’s password. We can weaponize this VIM vector to gain root privileges if the user runs VIM as root or uses the visudo command

VIM handles its configuration files differently for a user in a sudo context depending on the distribution of Linux. In some systems such as Ubuntu and Red Hat, VIM will use the current user’s .vimrc configuration file even in a sudo context. In other distributions, such as Debian, in a sudo context, VIM will use the root user’s VIM configuration

if the user runs VIM via sudo, our script being sourced will also run as root. Because of this, we will achieve root access without any extra effort. On a Debian or similar system that does not persist the user’s shell environment information when moving to a sudo context, we can add an alias to the user’s .bashrc file.

```
alias sudo="sudo -E"
```

An alias is just a shortcut to substitute a different command when a specific command is entered on the command line. The alias above replaces a standard sudo call with one that will force sudo to persist the user’s VIM settings. The shell script being loaded will then also run as root. We will need to source our .bashrc file from the command line if we want the alias changes to go into effect right away.

```
source ~/.bashrc
```
