[toc]

# L6.1 - Synchronization (3)

### Recap

#### Locks

```c
void thread(void) {
    lock();
      /* Critical section */
      ...
    unlock();
}
```

##### Atomic spinlocks

- Based on atomic test-and-set instruction
- Compatible with multiprocessor systems
- Accessible to user processes

```c
void spinlock_lock(int *lock) {
		while (test_and_set(lock) == 1);
}
```

```c
void spinlock_unlock(int *lock) {
		*lock = 0;
}
```

- But based on busy-waiting

`solution: Hardware Interrupts`

#### Semaphores

- Internal count
- `down()` decrements count by one, or blocks if count is 0
- `up()` increments count by one, and wakes up first blocked thread if any

##### Binary semaphore

```c
sem = sem_create(1);
void thread(void) {
    sem_down(sem);
    /* Critical section */
    ...
    sem_up(sem);
}
```

##### Counted semaphore

```c
sem = sem_create(0);
void thread1(void) { 
  x = get_packet();
  enqueue(q, x);
  up(sem);
} 
void thread2(void) {
  down(sem);
  x = dequeue(q);
  process_packet(x);
}
```



### Producer-consumer problem

#### Definition

Two or more threads communicate through a circular data buffer: some threads *produce* data that others *consume*.

<img src="Week 6.assets/Screen Shot 2023-05-16 at 3.50.24 AM.png" alt="Screen Shot 2023-05-16 at 3.50.24 AM" style="zoom:50%;" />

- Bounded buffer of size N
- Producers write data to buffer
  - Write at `in` and moves rightwards
  - Don't write more than the amount of available space
- Consumers read data from buffer
  - Read at `out` and moves rightwards
  - Don't consume if there is no data
- Allow for multiple producers and consumers

#### Solution 1: no protection

```c
int buf[N], in, out;
```

```c
void produce(int item)
{
    buf[in] = item;
    in = (in + 1) % N;
}
void thread_prod(void)
{
    while (1) {
        ...
        produce(x);
        ...
    }
}
```

```c
int consume(void)
{
    int item = buf[out];
    out = (out + 1) % N;
    return item;
}
void thread_cons(void)
{
    while(1) {
        ...
        x = consume();
        ...
    }
}
```

##### Issues

- Unprotected shared state
  - Race conditions on all shared variables
- No synchronization between consumers and producers

#### Solution 2: Lock semaphores

- Add protection of share state
  - Mutual exclusion around critical sections
  - Guarantees one producer and one consumer at a time	

```c
int buf[N], in, out;
sem_t lock_prod = sem_create(1), lock_cons = sem_create(1);
```

```c
void produce(int item)
{
    sem_down(lock_prod);
    buf[in] = item;
    in = (in + 1) % N;
    sem_up(lock_prod);
}
```

```c
int consume(void)
{
    sem_down(lock_cons);
    int item = buf[out];
    out = (out + 1) % N;
    sem_up(lock_cons);
    return item
}
```

#### Solution 3: Communication semaphores

- Add synchronization between producers and consumers
  - Producers wait if buffer is full
  - Consumers wait if buffer is empty

```c
int buf[N], in, out;
sem_t lock_prod = sem_create(1), lock_cons = sem_create(1);
sem_t empty = sem_create(N), full = sem_create(0);
```

```c
void produce(int item)
{
    sem_down(empty); 		//need empty spot
    sem_down(lock_prod);
    buf[in] = item;
    in = (in + 1) % N;
    sem_up(lock_prod);
    sem_up(full); 			//new item avail
}
```

```c
int consume(void)
{
    sem_down(full); 	//need new item
    sem_down(lock_cons);
    int item = buf[out];
    out = (out + 1) % N;
    sem_up(lock_cons);
    sem_up(empty); 		//empty slot avail
    return item
}
```



### Readers-writers problem

#### Definition

- Multiple threads access the same shared resource, but differently
  - *Many* threads only *read* from it
  - *Few* threads only *write* to it
- Readers vs writers
  - Multiple *concurrent* readers at a time
  - Or single writer at a time

##### Examples

- Airline ticket reservation
  - `Check the airline seats available - We are the Readers`
  - `Someone modify the system, buying a tickets - Wrtiers`
  - <img src="Week 6.assets/Screen Shot 2023-05-16 at 9.36.08 PM.png" alt="Screen Shot 2023-05-16 at 9.36.08 PM" style="zoom:50%;" />
- File manipulation
  - `Multiple processes modify the same file. Reading/writing`

#### Solution 1: Protect resource

```c
sem_t rw_lock = sem_create(1);
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
    sem_down(rw_lock);
    ...
    /* perform read */
    ...
    sem_up(rw_lock);
}
```

##### Analysis

- Mutual exclusion between readers and writers: **Yes**
- Only one writer can access the critical section: **Yes**
- Multiple readers can access the critical section at the same time: **No!**

#### Solution 2: Enable multiple readers

```c
int rcount = 0;
sem_t rw_lock = sem_create(1);
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
    rcount++;
    if (rcount == 1)
        sem_down(rw_lock);
    ...
    /* perform read */
    ...
    rcount--;
    if (rcount == 0)
    		sem_up(rw_lock);
}
```

##### Issue

- Race condition between readers on variable `rcount`!
  - `multiple thread could modify the global varialbe.`

#### Solution 3: Protect multiple readers

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

##### Analysis

- Correct solution
- But suffers from potential starvation of writers

### Semaphores

#### Concluding Notes

- Semaphores considered harmful (Dijkstra, 1968)
  - Simple algorithms can require more than one semaphore
    - Increase of complexity to manage them all
  - Semaphores are low-level primitives
    - Easy to make programming mistakes (e.g. down() followed by down())
  - Programmer must keep track of the order of all semaphores operations
    - Avoid deadlocks
  - Semaphores are used for both mutual exclusion and synchronization between threads
    - Difficult to determine which meaning a given semaphore has
- Need for another abstraction
  - Clear distinction between mutual exclusion and synchronization aspects
  - Concept of *monitor* developed in early 70's

### Synchronization barriers

#### Concept

- Enables multiple threads to wait until all threads have reached a particular point of execution before being able to proceed further

```c
void main(void)
{
    barrier_t b = barrier_create(3);
    thread_create(thread_func, b);
    thread_create(thread_func, b);
    thread_create(thread_func, b);
}
void thread_func(barrier_t b)
{
    while (/* condition */) {
        /* ... do some computation ... */
        ...
        /* Wait for the other threads */
        barrier_wait(b);
    }
}
```

<img src="Week 6.assets/Screen Shot 2023-05-16 at 10.07.21 PM.png" alt="Screen Shot 2023-05-16 at 10.07.21 PM" style="zoom:50%;" />

##### Implementation

- Using semaphores
- Using condition variables and the `broadcast()` feature

### Synchronization: the big picture

<img src="Week 6.assets/Screen Shot 2023-05-16 at 10.10.10 PM.png" alt="Screen Shot 2023-05-16 at 10.10.10 PM" style="zoom:50%;" />



# 5/10 L6.2 - Deadlocks (1)

### Deadlock examples

#### Real-life deadlock

<img src="Week 6.assets/Screen Shot 2023-05-16 at 10.27.54 PM.png" alt="Screen Shot 2023-05-16 at 10.27.54 PM" style="zoom:50%;" />

`I'm waiting for you, you wait for him, he wait for me... (死循环)`

#### Mutually recursive locking

```c
void thread1(void)
{
    lock(lock1);
    lock(lock2);
  
    ... /* computation */ ...
      
    unlock(lock2);
    unlock(lock1);
}
```

```c
void thread2(void)
{
    lock(lock2);
    lock(lock1);
  
    ... /* computation */ ...
      
    unlock(lock2);
    unlock(lock1);
}
```

- Will this code always reach completion?

##### Analysis

- Somewhat similar to race conditions
  - Will probably reach completion most of the time
  - But in rare cases, a certain order of execution will make the code deadlock
- Could be solved *simply by respecting consistent structure*
  - Core design pattern in concurrent programming
  - `In thread2, first lock(lock1) then lock(lock2)`

#### Dining Philosophers

<img src="Week 6.assets/Screen Shot 2023-05-16 at 10.45.27 PM.png" alt="Screen Shot 2023-05-16 at 10.45.27 PM" style="zoom:33%;" />

```c
sem_t fork[N];
void philosopher(int i)
{
    sem_t right = fork[i];
    sem_t left = fork[(i + 1) % N];
  
    while (1) {
        think();
        sem_down(right);	// Randomly, all of them loses the CPU after take their right fork.
        sem_down(left);
        eat();
        sem_up(left);
        sem_up(right);
    }
}
```

##### Analysis

- Deadlock if they're each able take their right fork
  - All waiting for their left fork to proceed

---

### Deadlock

#### Definition(s)

##### Starvation

- One or more tasks of a group are indefinitely blocked
  - Not able to make progress
  - These tasks are *starving*
- But, the group as a whole is still making progress

##### Deadlock

- Group of tasks is deadlocked if each task in the group is waiting for an event that only another task in the group can trigger
  - Usually, the event is the release of a currently held resource
  - None of the tasks can
    - run
    - releases resources
    - or be awakened
- A **deadlock** is the ultimate stage of **starvation**
