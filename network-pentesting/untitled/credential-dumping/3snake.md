# 3snake

Targeting rooted servers, reads memory from sshd and sudo system calls that handle password based authentication. Doesn't write any memory to the traced processes. Spawns a new process for every sshd and sudo command that is run. Listens for the proc event using netlink sockets to get candidate processes to trace.

 When it receives an sshd or sudo process ptrace is attached and traces read and write system calls, extracting strings related to password based authentication.

```text
git clone https://github.com/blendin/3snake.git
```

```text
make
./3snake -h
./3snake
```

Run in current terminal

```text
 ./3snake
```

Daemonize and dump output to file

```text
./3snake -d -o "/tmp/output_file.txt"
```





