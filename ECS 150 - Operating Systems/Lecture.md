## Lecture 1 - 4/3

##### Programming Projects

- 3 Projects to cover the main class topics
  - P1: Simple shell
  - P2: User-thread library + synchronization
  - P3: Custom file-system
- Have to be work in pair
- Group work
  - Self- and peer- evaluation
  - Individualize each partner project score

- Communication
  - Interview

##### Assessment

Theory 50%

- Class Quizzes
- Three Midterms
- Final Exam

Practice 50%

- Three projects
  - Graded both on correctness and quality of implementation

Midterm on next Friday !



##### What is an Operating System?

- Used to be a bit blurry 
  - Everything that was shipped by the operating system vendor 
  - But prone to abuse (e.g., US vs Microsoft, 98)

- Better Definition
  - An operating system (OS) is the layer of software that manages a computer's resources for its users and their applications.

<img src="Lecture.assets/Screen Shot 2023-04-03 at 2.37.00 PM.png" alt="Screen Shot 2023-04-03 at 2.37.00 PM" style="zoom:50%;" />

##### Computer Organization

- Types of computers
  - Desktop Computer
  - Blade server (by Dmitry Nosachev - CC BY-SA 4.0)
  - Internet of Things (by Miiicihiaieil Hieinizilieir, CC BY-SA 4.0) 
  - Car computer

- The bare minimum

  <img src="Lecture.assets/Screen Shot 2023-04-03 at 2.40.05 PM.png" alt="Screen Shot 2023-04-03 at 2.40.05 PM" style="zoom:50%;" />

- General-Purpose Computing

- Embedded Computing

<img src="Lecture.assets/Screen Shot 2023-04-03 at 2.42.39 PM.png" alt="Screen Shot 2023-04-03 at 2.42.39 PM" style="zoom:50%;" />

##### Hardware Components

###### Processor

<img src="Lecture.assets/Screen Shot 2023-04-03 at 2.43.55 PM.png" alt="Screen Shot 2023-04-03 at 2.43.55 PM" style="zoom:50%;" />

- Fetches instructions from memory, decodes them, and executes them 
  - Access to memory, arithmetic/logical operations, control flow
- Characterized by an Instruction Set (e.g. `x86_64`, `AArch64`, `RISC-V`)
- Contains a set of *registers* 
  - General-purpose registers, program counter, status register

###### Memory

- Set of **addressable bytes** that can hold numerical values

<img src="Lecture.assets/Screen Shot 2023-04-03 at 2.48.26 PM.png" alt="Screen Shot 2023-04-03 at 2.48.26 PM" style="zoom:50%;" />

- Organized in a hierarchy of layers (with various access times)

| Memory Layer         | Access Time         | Accessibility                                    |
| -------------------- | ------------------- | ------------------------------------------------ |
| CPU registers        | Immediate           | Via CPU instructions                             |
| CPU cache            | Few cycles          | Not addressable (transparent)                    |
| RAM/Main memory      | Few hundred cycles  | Addressable from CPU                             |
| Second-level storage | Few thousand cycles | Indirectly addressable (see later in the course) |

###### I/O Devices

- Controllers connected to system interconnect 
- Devices connected to controllers 
- The controller provides an interface for accessing the device resources/functionalities 
  - Via device registers

<img src="Lecture.assets/Screen Shot 2023-04-03 at 2.50.48 PM.png" alt="Screen Shot 2023-04-03 at 2.50.48 PM" style="zoom:75%;" />

<img src="Lecture.assets/Screen Shot 2023-04-05 at 1.44.13 PM.png" alt="Screen Shot 2023-04-05 at 1.44.13 PM" style="zoom:50%;" />

- Memory-mapped access
  - Device registers mapped into memory address space 
  - Accessible through regular memory instructions

- Port-mapped access 
  - Device registers mapped into special I/O space 
  - Accessible through special I/O instructions

###### Interconnects (buses, networks)

- Transfer data between components 
- Characterized by various features, speed, bandwidth, etc. 
- CPU to memory buses 
  - Cache bus (aka Back side bus in Intel lingo)
  - Memory bus (aka Front side bus in Intel lingo) 
    - Now implemented by AMD HyperTransport, or Intel QuickPath Interconnect 
- CPU to device buses

| Bus           | Created  | Bandwidth             | Type                     |
| ------------- | -------- | --------------------- | ------------------------ |
| ISA           | 1981     | ~8 MiB/s              | Expansion                |
| IDE           | 1986     | ~8 MiB/s              | Mass-storage             |
| PCI           | 1992     | ~133 MiB/s            | Expansion                |
| AGP           | 1997     | ~266 MiB/s            | Video card               |
| SATA 1.0      | 2000     | ~150 MiB/s            | Mass-storage             |
| PCI-e 1.x     | 2003     | ~250 MiB/s per lane   | Expansion/Video card     |
| **PCI-e 6.x** | **2022** | **~8 GiB/s per lane** | **Expansion/Video card** |





# Lecture 2 - 4/5

##### Recap

###### OS definition

- An operating system (OS) is the layer of software that manages a **computer's resources** for its users and their applications.

Computer's hardware

- CPU: executes instructions 
- Memory: holds addressable bytes
- I/O devices: provides extra features and connectivity
  - Configured and accessed via device registers
- Interconnect: transfers data between components



##### OS definition - Part 2

###### Roles

In order to manage a computer's resources, an OS needs to play various roles: 
- Referee
- Illusionist
- Glue

###### Design principles

A well-constructed OS needs to achieve various design goals: 
- Reliability
- Security 
- Portability 
- Performance



##### OS Roles

###### Referee

Manage the **resource sharing** between applications 
- Resource allocation (e.g., CPU, memory, I/O devices) 
  - `hardware is scarce`

- Isolation (e.g., fault isolation) 
- Communication (e.g., safe communication)

###### Illusionist
Abstraction of hardware via **resource virtualization** 
- Mask scarcity of physical resources
- Mask potential hardware failure

###### Glue

Set of **common services** to applications
- Hardware abstraction
- Filesystem, message passing, memory sharing (Hardrive)



##### OS design principles

###### Reliability

- OS operates its intended function without failure over time 
- Related to *availability*: OS is operational and available for use

###### Security

- OS is secure if cannot be compromised by a malicious attacker
- Related to *privacy*: data is only accessible by authorized users
  - `Prison example: Security is like the prison wall and guard. Privacy is like the who can get in/out, which area is prohibit/allow (like policy)`
- Enforcement mechanisms vs. security policies

###### Portability

- OS provides the same abstractions regardless of the underlying hardware
  - Applications 
  - System libraries 
  - Kernel

###### Performance

- Overhead
  - What the OS adds compared to a bare-metal execution
  - *Efficiency* is the opposite measure 
- Fairness
  - Fair distribution of resources
  - Doesn't mean *equal* since distribution can be priority-based 
- Latency
  - How long an operation takes 
- Throughput
  - How many operations can be executed within a period of time 

`Ferrarri vs. Truck: Ferrarri has better latency, but truck has more throughput.`

- Predictability
  - How consistent performance is over time

`we prefer consistent preformance, not sometimes good`



`App -> OS -> Hardware`

##### OS history

###### Three major phases

- Hardware is very expensive (1955-65) 
- Hardware slowly becomes affordable (1965-80) 
- Hardware becomes dirt cheap (1980-present)

<img src="Lecture.assets/Screen Shot 2023-04-05 at 2.37.03 PM.png" alt="Screen Shot 2023-04-05 at 2.37.03 PM" style="zoom:50%;" />

###### Hardware is very expensive (1955-65) 

- Introduction of the transistor in the mid-50s
- Expensive mainframes, operated by humans
- Read from punch card, run job, print result
- Batch systems in order to better serialize jobs

- Software
  - OS is simply a runtime *library* (common I/O functions) 
  - Applications have full access to hardware


###### Hardware slowly becomes affordable (1965-80)

- Use of integrated circuits

<u>Mainframe computers</u>

`Everyone in the city has a terminal connects to one big computer `

- E.g., IBM System/360

  <img src="Lecture.assets/Screen Shot 2023-04-05 at 2.43.05 PM.png" alt="Screen Shot 2023-04-05 at 2.43.05 PM" style="zoom:50%;" />

- Software

  - IBM OS/360
    - Multiprogramming, memory protection

  - Multics
    - Timesharing, dynamic linking, security, hierarchical file-system

<u>Personal Computers</u>

- E.g., DEC PDP-11

  <img src="Lecture.assets/Screen Shot 2023-04-05 at 2.45.30 PM.png" alt="Screen Shot 2023-04-05 at 2.45.30 PM" style="zoom:50%;" />

- Software
  - UNIX, BSD/SystemV variants	`Selling of UNIX code led to many variants of UNIX`
    - Creation of C language 
  - POSIX
    - Standardization of application/system interface

###### Hardware becomes dirt cheap (1980-present)

- Continuation of Moore's law 
- Personal computers
  - Command line interfaces
  - GUI
- Pervasive computers
  - Desktops, laptops 
  - Smartphones, tablets 
  - Embedded systems 
  - Data centers

###### Progression over time

| **Metric**| **1981** | **1997** | **2014**  | **Factor(2014/1981)** |
| ------------------------ | -------- | -------- | --------- | --------------------- |
| CPU performance (MIPS)   | 1        | 200      | 2500      | 2.5 K                 |
| CPU/computer             | 1        | 1        | 10+       | 10+                   |
| MIPS cost                | $100K    | $25      | $0.20     | 500 K                 |
| DRAM (MiB)/$             | $500     | $0.5     | $0.001    | 500 K                 |
| Disk (GiB)/$             | $333     | $0.14    | $0.00004  | 8M                    |
| WAN (bps)                | 300      | 256K     | 20M       | 60 K                  |
| LAN (bps)                | 10M      | 100M     | 10G       | 1000+                 |
| Ratio users to computers | 100:1    | 1:1      | 1:several | 100+                  |



##### Future of OSes

###### End of Moore's law

- Make better use of the same area
  - Deep redesign of processors?
- 3-D stacking
  - Multiprocessors 
  - Multi- and many-cores
- Quantum computing

###### From the very small... to the super big

- Power-efficient IoT devices
  - Smart home, smart city, smart [TBD]
- Giant data centers
  - Very large-scale storage

###### Heterogeneity

- Different processors on same chip
  - E.g., ARM big.LITTLE, Intel Hybrid Technology 
- Specialized computing accelerators
  - GP-GPUs, FPGAs, AI accelerators



## Lecture 3 - 4/7 

### 03. Syscall

##### C Standard Library

executable detector

<img src="Lecture.assets/Screen Shot 2023-04-07 at 2.22.09 PM.png" alt="Screen Shot 2023-04-07 at 2.22.09 PM" style="zoom:50%;" />

```c
#include <fcntl.h>
#include <stdio.h>
...
memset(buf, 0, sizeof(buf));
...
fd = open(argv[1], O_RDONLY);
if (fd < 0) {
	perror("open");
	exit(EXIT_FAILURE);
}
read(fd, buf, sizeof(buf));
close(fd);
...
printf("Executable detection... ");
...
printf("\n");
// exec_detector.c
// calling function that I have not written myself.
```

###### Declaration 

- Access via inclusion of headers 
  - Function prototypes 
  - Global variables (e.g. `errno`) 
  - Type definitions (e.g. `size_t`) 
  - Macros (e.g., `O_RDONLY`)

###### Definition

- Actual code via library 
- Linked at compile-time 
- Dynamically loaded at runtime

###### Categories of *libc* functions 

1. No privileged operation to perform 
2. Always needs to request privileged operation from OS 
   - Known as **system call**, or *syscall* 
3. Sometimes needs to request privileged operation from OS



##### Categories of *libc* functions 

###### 1. No privileged operation to perform (Regular Functions)

Usage example

```c
char buf[4];
...
memset(buf, 0, sizeof(buf));
```

- `memset() `only needs to have access to array `buf`
  - ` buf` is already part of the application's memory (here, defined in stack) 
  - No need for any special operations



###### 2. (Always) privileged functions

Usage example

```c
	fd = open(argv[1], O_RDONLY);
...
	read(fd, buf, sizeof(buf));
	close(fd);
```

- Need for special privileges: e.g., 
  - Verify that file exists on a physical medium accessible by computer (e.g., hard-drive, SD card, network, etc.) 
  - Check that current user has permission to open it with specified mode 
  - Actually read file's data from physical medium 
- Sensitive operations passed to OS 
  - Accessible via *syscalls*

`How did READ implemented?`

```c
#include <unistd.h>
#include <sys/syscall.h> // Syscall code numbers

ssize_t read(int fd, void *buf, size_t count) {
    long r = syscall3(SYS_read, fd, buf, count);
    if (r < 0) {
        errno = -r;
        return -1;
    }
    return r;
}
```

`things behind syscall3`

```assembly
static __inline long __syscall3(long n, long a1, long a2, long a3) {
    unsigned long ret;
    __asm__ __volatile__ ("syscall" : "=a"(ret)
                                    : "a"(n), "D"(a1),
                                      "S"(a2), "d"(a3)
                                    : "rcx", "r11",
                                      "memory");
    return ret;
}
```



###### 3. (Sometimes) privileged functions

```c
printf("Executable detection... ");
...
printf("\n");
```

- `printf()` prints to `stdout` which is internally buffered 
  - Flushed when buffer is full, or when encountering `\n `
- Flushing requires to OS to actually write the characters 
  - Use of (syscall) function `write()`

`If the printf sees a new line character / internal buffer is full, the printf flush the buffer to the OS. `

- Printf vs write

```c
printf("Hello ");
sleep(2);
printf("world!\n");

write(STDOUT_FILENO, "Hello ", 6);
sleep(2);
write(STDOUT_FILENO, "world!\n ", 7);
```

<img src="Lecture.assets/Screen Shot 2023-04-07 at 2.39.19 PM.png" alt="Screen Shot 2023-04-07 at 2.39.19 PM" style="zoom:50%;" />

`The first Hello did not send directly to the OS.`



##### System calls

###### Definition

- Specific CPU instruction 
- Immediate transfer of control to kernel (内核) code

<img src="Lecture.assets/Screen Shot 2023-04-07 at 2.41.09 PM.png" alt="Screen Shot 2023-04-07 at 2.41.09 PM" style="zoom:50%;" />

###### Purpose 

- Secure API between user applications and OS kernel

###### Main categories (Syscall)

- Process management 

- Files and directories 

- Pipes 

- Signals 

- Memory management



##### System Calls Categories

###### Process Management

Definition of a process 

- A process is a program in execution 
  - Each process is identified by its Process ID (PID) `Student ID`
  - Each process runs its own memory space `Every student thinks he/she is the only one in the school`
  - Each process is represented in the OS by a Process Control Block (PCB) `School's Student Management System`
    - Data structure storing information about process 
    - PID, state, CPU register copies for context switching, open files, etc.

<img src="Lecture.assets/Screen Shot 2023-04-07 at 2.43.45 PM.png" alt="Screen Shot 2023-04-07 at 2.43.45 PM" style="zoom:50%;" />

Main related functions/syscalls 

- Process creation and execution 
  - `fork()`: Create a new (clone) process 
  - `exec()`: Change executed program within running process 
- Process termination 
  - `exit()`: End running process 
  - `wait()/waitpid()`: Wait for a child process and collect exit code 
- Process identification 
  - `getpid()`: Get process PID 
  - `getppid()`: Get parent process PID

###### fork()

- Running process gets cloned into a *child* process 
- Child gets an (almost) identical copy of parent 
  - Same open files, command line arguments, memory, stack, etc. 
- Child "resumes" at the `fork()` as well

Example

```c
int a = 42;

int main(int argc, char *argv[]) {
    int b = 23;
    printf("Hello world!\n");
    fork();
    printf("My favorite number is %d.\n",
           argc + a + b);
    return 0;
}
```

`$ ./fork_101 Hello world! My favorite number is 66. My favorite number is 66.`

Distinguishing between parent and child (母体和克隆体)

- `fork()` returns a value 
  - PID of the *child* to the parent 
  - *zero* to the child
  - -1 to the parent in case of error

Example

```c
int main(void)
{
    pid_t pid;

    pid = fork();
    if (pid > 0)
        printf("I'm the parent!\n");
    else if (pid == 0)
        printf("I'm the child!\n");
    else
        printf("I'm the initial process! "
			   "But something went wrong...\n");

    printf("I'm here now, bye!\n");
    return 0;
}
```

- Output is not guaranteed

- Parent and child are independent processes 
- Scheduling is up to OS

Forking Process

<img src="Lecture.assets/Screen Shot 2023-04-07 at 2.58.04 PM.png" alt="Screen Shot 2023-04-07 at 2.58.04 PM" style="zoom:33%;" />



<img src="Lecture.assets/Screen Shot 2023-04-07 at 2.58.52 PM.png" alt="Screen Shot 2023-04-07 at 2.58.52 PM" style="zoom:33%;" />

<img src="Lecture.assets/Screen Shot 2023-04-07 at 2.59.23 PM.png" alt="Screen Shot 2023-04-07 at 2.59.23 PM" style="zoom:33%;" />



## 4/7 Discussion

###### exec() 

- Current process starts executing another *program* 
- Family of functions, with slight variations
  - `exec[lv]p?e?()` (see man page for details)

Example

```c
int main(void)
{
  char *cmd = "/bin/echo";
  char *args[] = { cmd, "ECS150", NULL};
  int ret;
  
  printf("Hi!\n");
  
  ret = execv(cmd, args);
  
  printf("Execv returned %d\n", ret);
  
  return 0;
}
```

- Result: `$ ./execv `

  `Hi! `

  `ECS150`

- Successful call to `exec()` never returns! 

- Otherwise returns `-1` and continues current program if it fails



###### exit()

- Termination of the current process 
- Ability to return an exit value

Example 

- Exit at any time during execution

```c
if (error)
	exit(1);
```

- Or return from main()

```c
int main(void) {
  ...
  return 0;
}
```

- Libc transparently exits

```c
exit(main(argc, argv));
```

<img src="Lecture.assets/Screen Shot 2023-04-07 at 4.20.48 PM.png" alt="Screen Shot 2023-04-07 at 4.20.48 PM" style="zoom:50%;" />

`0 means did not fail`



###### wait() / waitpid()

- `wait() `makes parent wait for *any* of its children to exit 
  - Parent is blocked from execution in the meantime 
- `waitpid()` enables more advanced options, such as 
  - Specify PID of child to wait for 
  - Don't block even if no children have exited yet

```c
pid = fork(); //child's pid is 0
if (pid != 0) {
    /* Parent */
    int status;
    wait(&status);
    /* == waitpid(pid, &status, 0) */
    printf("Child returned %d\n",
    				WEXITSTATUS(status));
} else {
      /* Child */
      printf("Will exit soon!\n");
      exit(42);
}
```



###### Putting it together: fork() + exec() + wait()

```c
int main(void)
{
	pid_t pid;
	char *cmd = "/bin/echo";
	char *args[] = { cmd, "ECS150", NULL};

	pid = fork();
	if (pid == 0) {
		/* Child */
		execv(cmd, args);
		perror("execv");
		exit(1);
	} else if (pid > 0) {
		/* Parent */
		int status;
		waitpid(pid, &status, 0);
		printf("Child returned %d\n",
			   WEXITSTATUS(status));
	} else {
		perror("fork");
		exit(1);
	}

    return 0;
}
```



##### The shell

- A shell is a user interface to run commands in the terminal 
- Typically makes heavy use of process-related functions



### Project 1

##### Shell, an introduction

###### What's a shell?

- User interface to the Operating System's services

- Gets input from user, interpret the input, and launch the desired action(s)

  <img src="Lecture.assets/Screen Shot 2023-04-07 at 4.35.29 PM.png" alt="Screen Shot 2023-04-07 at 4.35.29 PM" style="zoom:50%;" />

`What's run in the terminal. Understand your input`

###### Some big names

| Name | Comment | First released |
| ---- | ------- | -------------- |
|      |         |                |
|      |         |                |
|      |         |                |
|      |         |                |
|      |         |                |

- Big and old pieces of software
- Bash: 30+ years and ~200,000 lines of code!

##### Simple shell

Goal
- Understand important UNIX system calls
- Implementing a simple shell called sshell

Specifications
- Execute commands with arguments
     ` sshell@ucd$ date -u`
- Redirect standard output of command to file
`sshell@ucd$ date -u > file`
- Pipe the output of commands to other commands
`sshell@ucd$ cat /etc/passwd | grep root`
- Offer a selection of builtin commands
`sshell@ucd$ cd directory`
`sshell@ucd$ pwd`
`/home/jporquet/directory`
- And some extra feature(s)...
     - Combined redirection (>& + |&), simple variables ($a)4 / 32



##### Project assignment

- Project assignment published this morning!
- Read assignments multiple times and follow the instructions
  - Suggested phases to help with your progression
  - *Recommended* to follow but you don't have to
- Stay up-to-date
  - Extra information given during lecture or discussion
  - Class announcements
  - Piazza



##### Git



## Lecture 4 4/10 

### Recap

##### C standard library

- Non-privilieged functions
  - E.g. `memset()`
- Always/sometimes privileged functions
  - E.g. `read() / printf()`
  - require **system call**

##### Syscall categories

- **Process management**
  - A process is a program in execution
  - PCB data structure
- Files and directories, Pipes, Signals, Memory management

##### Process management

- `fork()`
  - Clone process
- `exec()`
  - Execute different program inside current process
- `exit()`
  - Terminate current process
  - return exit value
- `wait()`
  - Wait for child process to terminate
  - Collect exit status

#### Process management

##### getpid() / getppid()

- Notion of a (family) tree of processes

  - Only one parent per process
  - But possibly multiple children

- In Unix, `init` (PID=1) is ultimate ancestor

  - Only process created from scratch by kernel (and not by forking)

- `getpid()` returns process' PID

- `getppid()` return its parent's PID

  <img src="Lecture.assets/Screen Shot 2023-04-10 at 2.19.05 PM.png" alt="Screen Shot 2023-04-10 at 2.19.05 PM" style="zoom:30%;" />

##### Example

```c
int main(void){
  if (fork() > 0)
  /* Forces parent to wait for child
  * to force scheduling order */
  	wait(NULL);
  
  printf("My PID is %d\n", getpid());
  printf("My parent's PID is %d\n", getppid());
  
  return 0;
} //getpid.c
```

```bash
$ ./getpid
My PID is 406782
My parent's PID is 406781
My PID is 406781
My parent's PID is 162474 // The PID to the shell itself
```

```bash
$ echo $$
162474
```



#### System Calls - Files and directories

##### Files and directories

###### Concepts

- Files and directories in tree-like structure called Virtual File System
  - Internal nodes are directories, leaf nodes are files
- Every directory contains a list of filenames
- Every file contains an array of bytes

<img src="Lecture.assets/Screen Shot 2023-04-10 at 2.28.40 PM.png" alt="Screen Shot 2023-04-10 at 2.28.40 PM" style="zoom:50%;" />

- VFS can aggregate files and directories from various physical media (local hard-drive, remote network share, etc.)

###### Main related functions/syscalls

- File interaction
  - `open()`: open (create) file and return file descriptor
  - `close()`: close file descriptor
  - `read()`: read from file
  - `write()`: write to file
  - `lseek()`: move file offset
- File descriptor management
  - `dup2()`: duplicate file descriptor
- File characteristics
  - `stat()/fstat()`: get file status
- Directory traversal
  - `getcwd()`: get current working directory
  - `chdir()`: change directory
  - `opendir()`: open directory
  - `closedir()`: close directory
  - `readdir()`: read directory

##### Basic file interaction

- `open()` returns a **file descriptor** (FD), used for all interactions with file
  - Closed by `close()` when done with file
- `read()/write()` operations are sequential, tracked by file offset
- `lseek()` can manipulate current file offse

###### Example

```c
#include <fcntl.h>
...
int main(void)
...
	int fd;
...
  fd = open("file_101.c", O_RDONLY);

  read(fd, &c, 1);
  printf("%c\n", c);
  read(fd, &c, 1);
  printf("%c\n", c);

  lseek(fd, -2, SEEK_END);
  read(fd, &c, 1);
  printf("%c\n", c);

  close(fd);
...
}
```

```bash
$ ./file_101
#
i
}
```

##### File descriptors

###### Definition

- Table of open files per process
  - Part of PCB
  - (Duplicated upon forking)
- FDs are simple indexes in the table

<img src="Lecture.assets/Screen Shot 2023-04-10 at 2.35.05 PM.png" alt="Screen Shot 2023-04-10 at 2.35.05 PM" style="zoom:70%;" />

`0, 1 ,2 by default, already taken.`

###### Example

```c
int fd1, fd2, fd3;

fd1 = open("file_101.c", O_RDONLY);
fd2 = open("file_201.c", O_RDWR);

printf("fd1 = %d\n", fd1);
printf("fd2 = %d\n", fd2);
// file_201.c
```

```bash
$ ./file_201
fd1 = 3
fd2 = 4
...
```

###### Application

- `open()` always returns *first available* FD

```c
close(fd1);
fd3 = open("file_201.c", O_WRONLY);
printf("fd3 = %d\n", fd3);
// file_201.c
```

```bash
$./file_201
...
fd3 = 3
```

##### Standard streams

Initially, three open file descriptors per process

- 0: standard input (STDIN_FILENO)
- 1: standard output (STDOUT_FILENO)
- 2: standard error (STDERR_FILENO)	

<img src="Lecture.assets/Screen Shot 2023-04-10 at 2.44.11 PM.png" alt="Screen Shot 2023-04-10 at 2.44.11 PM" style="zoom:70%;" />

###### Example

```c
write(STDOUT_FILENO, "Hello ", 6);
write(1, "Hello ", 6);
fprintf(stdout, "Hello ");
printf("Hello\n");

write(STDERR_FILENO, "World ", 6);
write(2, "World ", 6);
fprintf(stderr, "World\n");
// standard_fds.c
```

```bash
$ ./standard_fds
Hello Hello Hello Hello
World World World
```

###### Redirections

- Can connect standard streams to other targets than the terminal

```bash
$ ./standard_fds > /dev/null
World World World
$ ./standard_fds 2> /dev/null | tr H J
Jello Jello Jello Jello
```

```bash
$ ./standard_fds >& myfile.txt
$ cat myfile.txt
Hello Hello World World World
Hello Hello
```

##### File descriptor manipulation

- `dup2()` replaces an open file descriptor with another

  - Note: avoide using deprecated `dup()`

  <img src="Lecture.assets/Screen Shot 2023-04-10 at 2.57.50 PM.png" alt="Screen Shot 2023-04-10 at 2.57.50 PM" style="zoom:50%;" />

###### Example

```c
nt main(void)
{
  int fd;
  
  printf("Hello #1\n");
  
  fd = open("myfile.txt", O_WRONLY | O_CREAT, 0644); 
  // || If file doesn't exist, create with 0644 permission
  // fd is equal to 3, since 0, 1, 2 are taken
  dup2(fd, STDOUT_FILENO);  // dup2(3, 1)
  // break the connection from 1 to terminal, now the connection created between 1 and myfile.txt
  // Now everything in printf() will go to that file instead of terminal.
  
  close(fd);
  
  printf("Hello #2\n");
  
  return 0;
}
// dup2.c
```

```bash
$ ./dup2
Hello #1
$ cat myfile.txt
Hello #2
```



# Lecture 5 - 4/12 

### Files and directories

##### File **characteristics**

- `stat()` returns information about a file, from a filename 
- Same with `fstat()`, but from an FD

###### Example

```c
int main(int argc, char *argv[])
{
    struct stat sb;
    stat(argv[1], &sb);
    printf("File type: ");
    switch (sb.st_mode & S_IFMT) {
        case S_IFDIR: printf("directory\n"); break;
        case S_IFREG: printf("regular file\n"); break;
        default: printf("other\n"); break;
}
    printf("Mode: %lo (octal)\n", (unsigned long) sb.st_mode);
    printf("File size: %lld bytes\n", (long long) sb.st_size);
    printf("Last file access: %s", ctime(&sb.st_atime));
    return 0;
} 
// stat.c
```

###### Output

```bash
$ ./stat stat.c
File type: regular file
Mode: 100644 (octal)
File size: 644 bytes
Last file access: Fri Sep 18 16:32:02 2020
$ ./stat .
File type: directory
Mode: 40755 (octal)
File size: 4096 bytes
Last file access: Fri Sep 18 11:59:49 2020
```

##### Directory traversal

- `getcwd()/chdir() `to access the current working directory
- `opendir()/closedir()/readdir()` to access the entries of a directory

###### Example

```c
int main(int argc, char *argv[])
{
    char cwd[PATH_MAX];
    DIR *dirp;
    struct dirent *dp;
  
    getcwd(cwd, sizeof(cwd));
    printf("Change CWD from '%s' to '%s'\n", cwd, argv[1]); chdir(argv[1]);
  
    dirp = opendir(".");
    while ((dp = readdir(dirp)) != NULL)
    		printf("Entry: %s\n", dp->d_name);
    closedir(dirp);
  
    return 0;
}
// dir_scan.c
```

###### Output

```bash
$ pwd
/home/jporquet
$ ./dir_scan /
Change CWD from '/home/jporquet' to '/'
Entry: ..
Entry: lib
Entry: home
Entry: etc
Entry: root
[...]
Entry: proc
Entry: tmp
Entry: dev
Entry: bin
Entry: sbin
Entry: mnt
Entry: .
Entry: usr
```



### Pipes

##### Example

```bash
michaelyang@campus-081-027 ~ % du -sh * |sort -hr | head -3
```

##### Definition

- Inter-process communication (IPC)
- Pipeline of processes chained via their standard streams
  - `stdout` of one process connected to `stdin` of next process

##### Details

- Internally implemented as anonymous files

  - Circular memory buffer of fixed size
  - Accessible via file descriptors and regular read/write transfers

- Processes run concurrently, implicitly synchronized by communication

  <img src="Lecture.assets/Screen Shot 2023-04-12 at 2.28.10 PM.png" alt="Screen Shot 2023-04-12 at 2.28.10 PM" style="zoom:50%;" />

#### Pipe()

- Create a pipe and return two file descriptors via array
  - [0] for reading access, [1] for writing access

###### Example

```c
int main(void)
{
    int fd[2];
    char send[7] = "Hello!";
    char recv[7];
  
    pipe(fd);
    printf("fd[0] = %d\n", fd[0]);
    printf("fd[1] = %d\n", fd[1]);
  
    write(fd[1], send, 7);
  
    read(fd[0], recv, 7);
    puts(recv);
  
    return 0;
}
```

###### Output

```bash
$ ./pipe
fd[0] = 3
fd[1] = 4
Hello!
```

<img src="Lecture.assets/Screen Shot 2023-04-12 at 2.40.41 PM.png" alt="Screen Shot 2023-04-12 at 2.40.41 PM" style="zoom:33%;" />

##### Process pipeline example

- Pseudo-code setting up two children as `command1 | command2`

```c
void pipeline(char *command1, char *command2)
{
    pid_t p1, p2;
    int fd[2];
  
    pipe(fd);
  
    if (!(p1 = fork())) { 					/* Child #1 */
        close(fd[0]); 							/* No need for read access */
        dup2(fd[1], STDOUT_FILENO); /* Replace stdout with pipe */
        close(fd[1]); 							/* Close now unused FD */
        exec(command1); 						/* Child #1 becomes command1 */
    }
    if (!(p2 = fork())) { 					/* Child #2 */
        close(fd[1]); 							/* No need for write access */
        dup2(fd[0], STDIN_FILENO); 	/* Replace stdin with pipe */
        close(fd[0]); 							/* Close now unused FD */
        exec(command2); 						/* Child #2 becomes command2 */
    }
    close(fd[0]); 									/* Pipe no longer needed in parent */
    close(fd[1]);
    waitpid(p1, NULL, 0); 					/* Parent waits for two children */
    waitpid(p2, NULL, 0);
}
```



### Midterms

Format:

5-6 questions in 30 minutes

1. concept of question and 2. snippets of code (understand and explain)

question from lecture slide

