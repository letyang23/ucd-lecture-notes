```c
int main(void)
{
  printf("plic\n");
  fork();
  printf("ploc\n");
  fork();
  printf("plac\n");
  
  return 0;
}
```

```makefile
%.o: %.s
		as -o $@ $<
```

```makefile
*.o: *.s
		as -o $@ $<
```

```makefile
%.o: %.s
		as -o %@ %<
```

```makefile
%.o: %.s
		as -o %.o %.s
```

```makefile
targets := myfact README.html
objs := main.o fact.o

CC := gcc
CFLAGS := -Wall -Wextra -Werror -MMD
CFLAGS += -g
PANDOC := pandoc

ifneq ($(V),1)
Q = @
endif

all: $(targets)

# Dep tracking *must* be below the 'all' rule
deps := $(patsubst %.o,%.d,$(objs))
-include $(deps)

myfact: $(objs)
    @echo "CC $@"
    $(Q)$(CC) $(CFLAGS) -o $@ $^
    
%.o: %.c
    @echo "CC $@"
    $(Q)$(CC) $(CFLAGS) -c -o $@ $<
    
%.html: %.md
    @echo "MD $@"
    $(Q)$(PANDOC) -o $@ $<
    
clean:
    @echo "clean"
    $(Q)rm -f $(targets) $(objs) $(deps)
```



| Task | Submission | Length |
| ---- | ---------- | ------ |
| A    | 0          | 24     |
| B    | 0          | 12     |
| C    | 4          | 4      |



```c
int rcount = 0;
sem_t rw_lock = sem_create(1), count_lock = sem_create(1);
```

```c
void writer(void)
{
    sem_down(rw_lock);
    ...
    /* perform write */
    ...
    sem_up(rw_lock);
}
```

```c
int reader(void)
{
    sem_down(count_lock);
    rcount++;
    if (rcount == 1)
        sem_down(rw_lock);
    sem_up(count_lock);
    ...
    /* perform read */
    ...
    sem_down(count_lock);
    rcount--;
    if (rcount == 0)
        sem_up(rw_lock);
    sem_up(count_lock);
}	
```



Consider the code below. Determine whether the following outputs are possible or not, when running this program.

```c
barrier_t b[2];

void thread1(void){
  printf("A1 ");
  barrier_wait(&b[0]);
  printf("B1 ");
  printf("C1 ");
  printf("D1 ");
}

void thread2(void){
  printf("A2 ");
  printf("B2 ");
  printf("C2 ");
  barrier_wait(&b[1]);
  printf("D2 ");
}

void thread3(void){
  printf("A3 ");
  printf("B3 ");
  barrier_wait(&b[0]);
  printf("C3 ");
  barrier_wait(&b[1]);
  printf("D3 ");
}

int main(void){
  barrier_init(&b[0], 2);
  barrier_init(&b[1], 2);
  
  thread_create(thread1);
  thread_create(thread2);
  thread_create(thread3);
  
  ...
  
  return 0;
}
```



Banker's algorithm -- Example

|                | Maximum Need | Currently allocated |
| -------------- | ------------ | ------------------- |
| Task A         | 9            | 3                   |
| Task B         | 4            | 2                   |
| Task C         | 7            | 2                   |
| Free resources | 10           | 3                   |

