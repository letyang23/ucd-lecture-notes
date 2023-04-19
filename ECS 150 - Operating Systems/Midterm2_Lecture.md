# 4/14 Lecture

### Recap

##### File characteristics

- stat()/fstat(): information about file
- getcwd()/chdir(): access the current working directory
- opendir()/closedir()/readdir(): access the entries of a directory

##### Pipes

- Typically used to chain processes to one another and create complex commands
  - Example: get three biggest items within current directory

```bash
$ du -sh * | sort -h -r | head -3
...
```

<img src="Midterm2_Lecture.assets/Screen Shot 2023-04-14 at 2.12.33 PM.png" alt="Screen Shot 2023-04-14 at 2.12.33 PM" style="zoom:50%;" />

### Signals

##### Definition

- Form of inter-process communication (IPC)
- Software notification system
  - From process' own actions: e.g., `SIGSEGV` (Segmentation fault)
  - From external events: e.g., `SIGINT` (Ctrl-C)
- About 30 different signals (see `man 7 signal`)

##### Default action

In case process does not define specific signal handling

- Terminate process: e.g., `SIGINT`,` SIGKILL`*
- Terminate process and generate core dump: e.g., `SIGSEGV`
- Ignore signal: e.g., `SIGCHLD`
- Stop process: e.g., `SIGSTOP`*
- Continue process: e.g., `SIGCONT`

##### Handling or ignoring

- Possible to change default action (but not for all signals!)
- Ignore signals
  - Mask of blocked signals
- Set signal handlers
  - Function to be run upon signal delivery

##### Main related functions/syscalls

- Sending signals
  - `raise()`: Send signal to self
  - `kill()`: Send signal to other process
  - `alarm()/setitimer()`: Set timer for self
    - Receive signal (`SIGALRM/SIGVTALRM`) when timer is up
- Blocking signals
  - `sigprocmask()`: Examine or change signal mask
  - `sigpending()`: Examine pending blocked signals
- Receiving signals
  - `sigaction()`: Map signal handler to signal
    - Also `signal()` but not recommended
  - `pause()`: Suspend self until signal is received

###### Example

```c
void alarm_handler(int signum)
{
		printf("\nBeep, beep, beep!\n");
}
int main(void)
{
    struct sigaction sa;
    sigset_t ss;
  
    /* Ignore Ctrl-C */
    sigemptyset(&ss);
    sigaddset(&ss, SIGINT);
    sigprocmask(SIG_BLOCK, &ss, NULL);
  
    /* Set up handler for alarm */
    sa.sa_handler = alarm_handler;
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    sigaction(SIGALRM, &sa, NULL);
  
    /* Configure alarm */
    printf("Alarm in 5 seconds...\n");
    alarm(5);
  
    /* Wait until signal is received */
    pause();
  
    /* Bye, ungrateful world... */
    raise(SIGKILL);
  
    return 0;
} 
// signal.c
```



###### Result

```bash
$ ./signal
Alarm in 5 seconds...
^C^C^C^C
Beep, beep, beep!
zsh: killed 	./signal
```



### Memory management

#### Division of labor

##### User C library

- `malloc()/free()` for dynamic memory allocation
  - **Heap** memory segment (at the end of **data** segment)
- Fine-granularity management only
  - When heap is full, syscall to kernel to request for more

##### OS/Kernel

- Memory management at "page" level
- Allocation of big chunks (many pages) to user library

#### Related functions/syscalls

- `sbrk()/brk()`
  - Linear increase of data segment
  - Old way of allocating heap space
  - Legacy function now

- `mmap()`
  - Map pages of memory in process' address space
  - Can also map a file's contents
  - Extremely versatile and powerful function

```c
#include <fcntl.h>
#include <signal.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>

void sigint_handler(int signum)
{
	printf("Let's resume!\n");
}

int main(void)
{
	int fd_file, fd_pipe[2];
	char buf[256];

	/* Set signal handler for SIGINT (Ctrl-C) */
	signal(SIGINT, sigint_handler);

	/* First pause */
	printf("Check usual file descriptors\n");
	pause();

	/* Now, let's add some new file descriptors */
	fd_file = open("log_err.txt", O_CREAT | O_RDWR, 0644);
	pipe(fd_pipe);

	/* Second pause */
	printf("Check with additional file descriptors\n");
	pause();

	/* Now, let's make a few changes */
	dup2(fd_pipe[0], STDIN_FILENO);
	dup2(fd_file, STDERR_FILENO);

	close(fd_pipe[0]);
	close(fd_file);

	/* Third pause */
	printf("Check after modifying file descriptors\n");
	pause();

	/* Test our weird scenario */
	write(fd_pipe[1], "Hello!\n", 8);
	fgets(buf, 256, stdin);
	printf("We received something, printing it to stderr!\n");
	fprintf(stderr, "%s", buf);

	return 0;
}

// file_descriptors.c
```



## 04. OS Structure

### OS Layers: overview

<img src="Midterm2_Lecture.assets/Screen Shot 2023-04-14 at 2.46.26 PM.png" alt="Screen Shot 2023-04-14 at 2.46.26 PM" style="zoom:30%;" />

##### Applications

- User function calls
- Written by programmers
- Compiled by programmers

##### Libraries

- Definition
  - Via standard headers (e.g., `stdio.h, stdlib.h, math.h`)
  - Used like regular functions
- Declaration
  - Pre-compiled objects (e.g., `libc.so.6, libc.a, libm.so`)
  - Input to linker (e.g., `gcc -lc -lm`)
- Code inclusion
  - Included in executable directly (e.g., `gcc -static`)
  - Or resolved at load-time

#### All about applications + libraries

##### Application compilation

<img src="Midterm2_Lecture.assets/Screen Shot 2023-04-14 at 2.52.32 PM.png" alt="Screen Shot 2023-04-14 at 2.52.32 PM" style="zoom:40%;" />

GCC can pre-process, compile, assemble and link together

- Preprocessor (`cpp`) transform program before compilation 
- Compiler (`cc`) compiles a program into assembly code
- Assembler (`as`) compiles assembly code into relocatable object file
- Linker (`ld`) links object files into an executable

```bash
$ % gcc hello.c -o hello -save-temps
$ % ll
```



### Project 1

- Overview:
  - Autograding is 60%
  - capture **correctness**

- Complete grading test scripts has >25 test cases, and 8 of them will be published.
- All the test cases can be reproduced manually.
- Run it first before continue. Re-read the assignment prompt. Error handling is important.



Manual Review

- 40%

- quality of project

- Various aspects related to real-life programming projects

  - Project well-packaged

  - Code properly designed and implemented

  - Code well-explained

  - ```c
    fprintf(stderr, " Error: command not found");
    //Use enum
    
    ```

  - 



# 4/19 Lecture

### 05. The Kernel Abstraction

##### Process abstraction

###### Process definition

- A process is a program in execution

  <img src="Midterm2_Lecture.assets/Screen Shot 2023-04-19 at 2.13.02 PM.png" alt="Screen Shot 2023-04-19 at 2.13.02 PM" style="zoom:40%;" />

##### Lack of protection

- Multiple processes can be loaded in memory and run concurrently

###### Issues

- Buggy process
  - Crash other processes
  - Crash the OS
  - Hog all the resources
- Malicious process

###### Solution

- Redefine process abstraction
- Include notion of protection

<img src="Midterm2_Lecture.assets/Screen Shot 2023-04-19 at 2.16.42 PM.png" alt="Screen Shot 2023-04-19 at 2.16.42 PM" style="zoom:40%;" />

##### Process RE-definition

-  A process is a program in execution, running with limited rights

###### Protected execution

- Memory segments process can access
- Other permissions process has
  - E.g., what files it can access
  - Based on process' user ID, group ID

<img src="Midterm2_Lecture.assets/Screen Shot 2023-04-19 at 2.17.53 PM.png" alt="Screen Shot 2023-04-19 at 2.17.53 PM" style="zoom:50%;" />

###### But efficient

- Restricting rights must not hinder functionality
- Efficient use of hardware
- Communication with OS and between processes is safe

##### Limited priviledge execution

###### Interpreted execution

- Basic model in interpreted languages
  - Javascript, Python, etc.
- Emulate each program instruction
  - If instruction is permitted, then perform it
  - Otherwise, stop process
- But execution quite slow...

`I drive the car to take my friend around `

<img src="Midterm2_Lecture.assets/Screen Shot 2023-04-19 at 2.21.37 PM.png" alt="Screen Shot 2023-04-19 at 2.21.37 PM" style="zoom:40%;" />

###### Native execution

- Run unprivileged code directly on the CPU
  - Very fast execution
- But safe execution needs specific hardware support...

<img src="Midterm2_Lecture.assets/Screen Shot 2023-04-19 at 2.22.43 PM.png" alt="Screen Shot 2023-04-19 at 2.22.43 PM" style="zoom:40%;" />

`Your friend might crash your car.`



#### Dual-mode operation

##### Concept

- Distinct execution modes supported directly in hardware
  - Indicated by a bit in processor status register (e.g., 0 or 1)
  - *Can be more than one mode on some processor architectures*

###### Kernel mode

- Execution with full privileges on the hardware
  - Read/write to any memory location
  - Access to any I/O device
  - Read/write to any disk sector
  - Send/receive any packet
  - Etc.

###### User mode

- Limited privileges on the hardware
  - As granted by the operating system

<img src="Midterm2_Lecture.assets/Screen Shot 2023-04-19 at 2.25.02 PM.png" alt="Screen Shot 2023-04-19 at 2.25.02 PM" style="zoom:40%;" />

##### Hardware support
###### Privileged instructions
- Potentially unsafe instructions prohibited when in user mode

- Only available in kernel mode

  `example: shut-down instruction. WFI(wait for interrupt) on RISC-V.`
###### Memory protection
- Memory accesses outside of process' memory limits prohibited
- Prevent process from overwriting kernel's or other processes' memory
###### Timer interrupts
- Kernel periodically regains control on CPU
- Prevent running process from hogging hardware
###### Mode switch
- Safe and efficient way to switch mode
  - From user mode to kernel mode, and vice-versa