[toc]

# L5.3 - Synchronization (2)

### Recap

#### Race condition

- Output of concurrent program depends on order of operations between threads

```c
void thread_a(void){
  x = x + 1;
}
void thread_b(void){
  x = x + 2;
}
```

```bash
$ ./a.out
3
$ ./a.out 		# Very rare but possible...
2
$ ./a.out 		# Very rare but possible...
1
```

- Indeterministic concurrent execution
  - Thread scheduling

    <img src="Week 6.assets/Screen Shot 2023-05-25 at 11.02.17 AM.png" alt="Screen Shot 2023-05-25 at 11.02.17 AM" style="zoom:50%;" />
  - Instruction reordering
  - Multi-word operations

#### Critical section

```c
void thread_1(void) {
  note1 = 1; 							// v
  while (note2 == 1) 			// CS_enter()
  		; // ^
  if (milk == 0) { 				// v
  		milk++; 						// CS
  } 											// ^
  note1 = 0; 							// CS_exit()
}

void thread_2(void) {
  	note2 = 1; 						// CS_enter()
  	if (note1 == 0) { 		// ^
  			if (milk == 0) { 	// v
  					milk++; 			// CS
  		} 									// ^
  }
  note2 = 0; 							// CS_exit()
}
```

- Shared variable
- Safety property
- Liveness property
- Mutual exclusion



### Locks

#### Definition

- A lock is a *synchronization* variable that provides *mutual exclusion*
- Two states: *locked* and *free* (initial state is generally *free*)

##### API

- `lock()` or `acquire()`
  - Wait until lock is free, then grab it
- `unlock()` or `release()`
  - Unlock, and allow one of the threads waiting in acquire to proceed

```c
int milk;
int main(void)
{
  thread_create(roommate_fcn);
  thread_create(roommate_fcn);
  ...
}
```

```c
void roommate_fcn(void)
{
  ...
  lock();
  
  /* Critical section */
  if (!milk)
    milk++;
  
  unlock();
  ...
}
```

#### Simple uniprocessor implementation

- Race conditions are coming from indeterministic scheduling
  - Breaks atomicity of instruction sequence
  - Caused by preemption (i.e. timer interrupt)
- Solution: disable the interrupts!

```c
void lock(){
  disable_interrupts();
}
```

```c
void lock(){
  enable_interrupts();
}
```

##### Issues

- Only works on uniprocessor systems
  - `If you have multiple processors (like two CPU), it won't work`
- Dangerous to have unpreemptable code
  - `critical section if has bug like infinite loop`
- Cannot be used by user applications
  - `User application cannot disable interrupts`

### Multiprocessor spinlocks

#### Hardware support
- *Test-and-set* hardware primitive to provide mutual exclusion
  - a.k.a *Read-modify-write* operation
- Typically relies on a multi-cycle bus operation that **atomically** reads and updates a memory location
  - Multiprocessor support

```c
/* Equivalent of a test&set hardware instruction in software */
ATOMIC int test_and_set(int *mem){
  int oldval = *mem;
  *mem = 1;
  return oldval;
}
```

<img src="Week 6.assets/Screen Shot 2023-05-25 at 4.57.45 PM.png" alt="Screen Shot 2023-05-25 at 4.57.45 PM" style="zoom:50%;" />

##### Lock implementation

```c
void spinlock_lock(int *lock)
{
		while (test_and_set(lock) == 1);
}
```

```c
void spinlock_unlock(int *lock)
{
		*lock = 0;
}
```

#### Revisiting "Too Much Milk!"

```c
int lock = 0;
```

##### Thread 1

```c
spinlock_lock(&lock);
if (milk == 0) {
  milk++;
}
spinlock_unlock(&lock);
```

##### Thread 2

```c
spinlock_lock(&lock);
if (milk == 0) {
  milk++;
}
spinlock_unlock(&lock);
```

##### Thread 3

```c
spinlock_lock(&lock);
if (milk == 0) {
  milk++;
}
spinlock_unlock(&lock);
```

##### Thread 4

```c
spinlock_lock(&lock);
if (milk == 0) {
  milk++;
}
spinlock_unlock(&lock);
```

`This solution will work`

#### Issue

- Busy-waiting wastes cpu cycles
  - Only to reduce latency

```c
void spinlock_lock(int *lock)
{
  while (test_anzd_set(lock) == 1);
}
// Spin for nothing. Waste cpu cycle. 
// 不会延迟，因为一放lock另外一个cpu马上take over 
```

#### Solution

##### "Cheap" busy-waiting

- Yield/sleep when unable to get the lock, instead of looping

```c
void lock(int *lock)
{
  while (test_and_set(lock) == 1)
    thread_yield(); 	//or, sleep(N);
}
```

###### Cons

- Yielding still wastes cpu cycles
- Sleeping impacts latency as well

##### Better primitives

- *Block* waiting threads until they can proceed

```c
void lock(int *lock)
{
  while (test_and_set(lock) == 1)
    thread_block(lock);
}
```

###### Examples

- Semaphores
- Mutexes (equivalent to binary semaphore with the notion of ownership)



### Semaphores

#### Definition

- Invented by Dijkstra in the 60's
- A semaphore is a generalized lock
  - Used for different types of synchronization (including mutual exclusion)
  - Keeps track an arbitrary resource count
    - Queue of threads waiting to access resource

<img src="Week 6.assets/Screen Shot 2023-05-25 at 5.28.30 PM.png" alt="Screen Shot 2023-05-25 at 5.28.30 PM" style="zoom:50%;" />

#### API

- Initial count value (but not a maximum value)

```c
sem = sem_create(count);
```

- `down()` or `P()`
  - Decrement by one, or block if already 0
- `up()` or `V()`
  - Increment by one, and wake up one of the waiting threads if any

##### Possible implementation:

```c
void sem_down(sem)
{
  spinlock_lock(sem->lock);
  while (sem->count == 0) {
    /* Block self */
    ...
}
  sem->count -= 1;
  spinlock_unlock(sem->lock);
}
```

```c
void sem_up(sem)
{
  spinlock_lock(sem->lock);
  sem->count += 1;
  /* Wake up first in line */
  /* (if any) */
  ...
  spinlock_unlock(sem->lock);
}
```

#### Binary semaphore

- Semaphore which count value is either 0 or 1
- Can be used similarly as a lock
  - But no busy waiting, waiting thread are blocked until they can get the lock
- Guarantees mutually exclusive access to a shared resource
- Initial value is generally 1 (ie *free*)

##### Example

```c
sem = sem_create(1);
```

###### Thread 1

```c
...
down(sem);
		Critical section
up(sem);
...
```

###### Thread 2

```c
...
down(sem);
		Critical section
up(sem);
...
```

*Mutexes are a similar concept except that they also have the notion of ownership*



#### Counted semaphore

- Semaphore which count value can be any positive integer
  - Represents a resource with many "units" available
- Initial count is often the number of initial resources (if any)
- Allows a thread to continue as long as enough resources are available
- Used for synchronization

##### Example

```c
sem_packet = sem_create(0);
```

###### Thread 1

```c
while (1) {
    x = get_network_packet();
    enqueue(packetq, x);
    up(sem_packet);
}
```

###### Thread 2

```c
while (1) {
    down(sem_packet);
    x = dequeue(packetq);
    process_contents(x);
}
```



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

- One or more tasks of a group are indefinitely blocked `Not everybody`
  - Not able to make progress
  - These tasks are *starving*
- But, the group as a whole is still making progress

##### Deadlock

- Group of tasks is deadlocked if each task in the group is waiting for an event that only another task in the group can trigger `Everybody starving`
  - Usually, the event is the release of a currently held resource
  - None of the tasks can
    - run
    - releases resources
    - or be awakened
- A **deadlock** is the ultimate stage of **starvation**

`For deadlock not happen, we need to proper design the synchronization in the first place. It is very hard to test.`

#### Resource allocation graph

##### Formalism

- Task A is *holding* resource R

<img src="Week 6.assets/Screen Shot 2023-05-25 at 5.48.47 PM.png" alt="Screen Shot 2023-05-25 at 5.48.47 PM" style="zoom:50%;" />

- Task B is *requesting* resource

<img src="Week 6.assets/Screen Shot 2023-05-25 at 5.49.56 PM.png" alt="Screen Shot 2023-05-25 at 5.49.56 PM" style="zoom:50%;" />

##### Example

- A is requesting S while holding R
- B is requesting R while holding S

<img src="Week 6.assets/Screen Shot 2023-05-25 at 5.51.52 PM.png" alt="Screen Shot 2023-05-25 at 5.51.52 PM" style="zoom:30%;" />

- Cycle in the resource allocation graph expresses a **deadlock** !

#### Four conditions describing a deadlock

1. **Mutual exclusion/bounded resources**
   - A resource is assigned to exactly one task at a time
   - `Road is not infinite. `

2. **Hold and wait**
   - Each task is holding a resource while waiting for another resource
   - `Car needs to hold a spot. Car needs to move (acquire) forward spot`

3. **No preemption**
   - Resources cannot be preempted
   - `The bus cannot be moved up in the air to free the road.`

4. **Circular wait**
   - One task is waiting for another in a circular fashion
   - `Everybody is waiting for one another`

When a deadlock occurs, it means that **all four** conditions are met

<img src="Week 6.assets/Screen Shot 2023-05-25 at 5.54.42 PM.png" alt="Screen Shot 2023-05-25 at 5.54.42 PM" style="zoom:50%;" />

### Deadlock strategies

#### Dealing with deadlocks

- **1. Ostrich algorithm**

  <img src="Week 6.assets/Screen Shot 2023-05-25 at 6.00.59 PM.png" alt="Screen Shot 2023-05-25 at 6.00.59 PM" style="zoom:50%;" />

  - Just ignore the problem altogether
  - Reset when it happens

- **2. Detection and recovery**

  <img src="Week 6.assets/Screen Shot 2023-05-25 at 6.01.19 PM.png" alt="Screen Shot 2023-05-25 at 6.01.19 PM" style="zoom:50%;" />

  - Let the problem occurs
  - Fix it afterwards

- **3. Dynamic avoidance**

  <img src="Week 6.assets/Screen Shot 2023-05-25 at 6.01.35 PM.png" alt="Screen Shot 2023-05-25 at 6.01.35 PM" style="zoom:50%;" />

  - Careful resource allocation during execution
  - Avoid having the problem

- **4. Prevention**

  <img src="Week 6.assets/Screen Shot 2023-05-25 at 6.01.45 PM.png" alt="Screen Shot 2023-05-25 at 6.01.45 PM" style="zoom:50%;" />

  - Negating one of the four necessary conditions so that, by design, the problem cannot happen


#### 1. Ostrich algorithm

*A.k.a burying one's head in the sand and pretending there is no problem*

##### Idea

<img src="Week 6.assets/Screen Shot 2023-05-25 at 6.03.34 PM.png" alt="Screen Shot 2023-05-25 at 6.03.34 PM" style="zoom:30%;" />

- If the OS kernel locks up
  - Reboot!
- If an application hangs
  - Kill the application and restart!
- Etc.

##### Mitigation

- Make it less painful for users
  - "Autosave" principle `You will not lose everything if you deadlock`
- If an application runs for a while and then hangs
  - Checkpoint the application
  - Change the environment (reboot?)
  - Restart the application from the checkpoint

#### 2. Detection and recovery

##### Idea

- Let deadlocks happen and then deal with the situation
  - Need to detect deadlock first
  - Once deadlock is detected, recover from it

##### 1. Detection

<img src="Week 6.assets/Screen Shot 2023-05-25 at 6.09.31 PM.png" alt="Screen Shot 2023-05-25 at 6.09.31 PM" style="zoom:50%;" />

- Track resource ownerships and requests
- If a cycle can be found within the graph, then there is a deadlock

`Analyze the graph to see if there is a cycle`

##### 2. Recovery

- Terminate one task
  - Choose task that can be rerun from the beginning 
- Rollback one task
  - Regular checkpoint and restart from it
- Resource preemption
  - Take missing resource from another task
  - Very difficult in practice

####  3. Dynamic avoidance

- When a resource is requested, resource is granted only if it maintains the system in a *safe state*

##### States

<img src="Week 6.assets/Screen Shot 2023-05-25 at 9.35.45 PM.png" alt="Screen Shot 2023-05-25 at 9.35.45 PM" style="zoom:50%;" />

- **Safe state**
  - For any possible sequence of resource requests, there exists at least one safe sequence of processing the requests that eventually succeeds in granting all pending and future requests.
- **Unsafe state**
  - In an unsafe state, there exists at least one sequence of pending and future resource requests that leads to deadlock no matter what processing order is tried.
- **Deadlock**

##### Banker's algorithm

1. State maximum resource needs in advance
2. Allocate resources dynamically, when resource is requested
   - Wait if request is impossible
   - Wait if request would lead to unsafe state
3. Request can be granted only if there exists a sequential ordering of threads that is deadlock free

##### Banker's algorithm -- Example

|                    | Maximum Need | Currently allocated |
| ------------------ | ------------ | ------------------- |
| Task A             | 9            | 3                   |
| Task B             | 4            | 2                   |
| Task C             | 7            | 2                   |
| **Free resources** | 10           | 3                   |

- B requests 2 resources: should grant request or not?
  - `B+2 = 4  currently allocated, only 1 free resources`
  - ` In worst case, Task A need max, C need max.`
  - `B reach end, A and C need wait. B will release 5 resources`
  - `C will then need 5 more resource to max. A still waiting`
  - `C then complete and all of resources give to A, then it done.`
  - `So yes, the request would be granted.`
- A requests 1 resources: should grant request or not?
  - `A+1 = 4 resources. Worst scenario: A need 5 more, B need 2 more, C need 5 more (all to max). And there is 2 more resources free. B would reach completion first, and release 4 resources. However, in this time, A need 5 more resources, c need 5 more resources. Impossible for both of them reach completion.`
  - `So, No, might end in deadlock.`

###### Scalability

For tracking multiple resources:

- Two 2-D matrices
  - Allocated
  - Max needed
  - `In practice it would really difficult algorithm to run properly`



# 5/12 L6.3 - Deadlocks (2)

### Recap

#### Deadlock strategies

- Ostrich algorithm
  - Don't deal with the problem
  - Terminate deadlocked system and restart
- Detection and recovery
  - Let deadlock happen
  - Detect it and recover from it
- Dynamic avoidance
  - Carefully allocate resources in such a way to avoid deadlocks
- Prevention
  - Make system deadlock-free by design
  - Negate one of the four conditions

### 4. Prevention

<img src="Week 6.assets/Screen Shot 2023-05-25 at 9.55.39 PM.png" alt="Screen Shot 2023-05-25 at 9.55.39 PM" style="zoom:33%;" />

By *design*, **invalidate** one of the four conditions:

1. Mutual exclusion/bounded resources
2. Hold and wait
3. No preemption
4. Circular wait

`Redesign the code.`

#### Invalidate "Mutual exclusion/bounded resources"

##### More resources

```c
sem_t fork[N + 1];
// Instead of 5 forks, we increase to 6 forks
```

`Fix to dining philosophers problem`

- Increase number of available resources

##### Virtualization

- Virtualize resources

  - E.g., printer queue

    <img src="Week 6.assets/Screen Shot 2023-05-25 at 9.57.32 PM.png" alt="Screen Shot 2023-05-25 at 9.57.32 PM" style="zoom:50%;" />

##### Lock-free resources

<img src="Week 6.assets/Screen Shot 2023-05-25 at 9.57.43 PM.png" alt="Screen Shot 2023-05-25 at 9.57.43 PM" style="zoom:50%;" />

- Make resources sharable without mutual exclusion requirement
  - Lock-free data structures

#### Invalidate "Hold and wait"

- Don't hold resources when waiting for another

##### Two-phase locking

- Phase 1:
  - Try to lock all required resources before proceeding
- Phase 2:
  - If successful, use needed resources and release
  - Otherwise, release all resources and try acquiring them again

##### Example

```c
while (1) {
  think();
  sem_down(right);
  if (!sem_try_down(left)) {
    sem_up(right);
    continue;
    // Try to get the left fork. If not successful, release the right fork and go back to the beginning.
  }
  eat();
  sem_up(left);
  sem_up(right);
}
```

`Fix to dining philosophers problem`

##### Issues

- Starvation if task is never able to grab all the resources it needs
- Complicated to manage when the amount of resources grows

#### Invalidate "No preemption" (of resources)

- Make *resources* preemptable by runtime system
  - Preempt requesting tasks' resources if all not available
  - Preempt resources of waiting tasks to satisfy request
- Only works when it's easy to save and restore state of resource
  - Memory page via swapping
  - CPU via context switching
- In most cases, *impossible*...

#### Invalidate "Circular wait"

- Impose an order of requests for all resources
  - Assign a unique ID to each resource
  - All requests must be in ascending order of the IDs

##### Rationale

Intuition is that a cycle requires

- Edge from high to low node

- Edge to same node

  <img src="Week 6.assets/Screen Shot 2023-05-25 at 10.08.52 PM.png" alt="Screen Shot 2023-05-25 at 10.08.52 PM" style="zoom:50%;" />

  `Lower number first and higher number second`

##### Example

```c
sem_t fork[N];

void philosopher(int i)
{
  sem_t right = fork[i];
  sem_t left = fork[(i + 1) % N];
  
  while (1) {
    think();
    if ((i + 1) % N < i) {
      // Only for last philosopher
      sem_down(left);
      sem_down(right);
    } else {
      // Everyone else
      sem_down(right);
      sem_down(left);
    }
    eat();
    sem_up(left);
    sem_up(right);
  }
}
```

`Fix to dining philosophers problem`



### Livelock

#### Principle

`Hallway, both of them move at same time and stuck again, both move again and stuck again...`

Variant of deadlock where two or more tasks are continuously changing their state in response to the other task(s) without doing any useful work.

- Similar to deadlock: no progress
- But no task is blocked waiting for anything
- Can be a risk with detection and recovery algorithms...

### Real-life strategies

##### Ostrich algorithm

<img src="Week 6.assets/Screen Shot 2023-05-25 at 10.15.16 PM.png" alt="Screen Shot 2023-05-25 at 10.15.16 PM" style="zoom:50%;" />

- Most systems implement Ostrich approach

##### Dynamic avoidance

- A couple of POSIX functions can fail with `EDEADLK` errno
  - File-locking (POSIX `lockf()`)
  - Relocking a mutex (`pthread_mutex_lock()`)

##### Detection and recovery/dynamic avoidance

- Some specialized systems have elaborate mechanisms
  - Database, banking

##### Prevention

- Design concurrent code well (enough resources, ensure consistency, etc.)
- Avoid deadlocks by not having locks in the first place!
  - Lock-free data structures
  - Transactional memory

#### Lock-free data structures

- Often based on *compare-and-swap*-like atomic instructions (similar to *test-and-set* instructions)

```c
// Equivalent of a CAS hardware instruction in software
ATOMIC int cas(int *mem, int old, int new)
{
  if (*mem != old)
    return 0;
  *mem = new;
  return 1;
}
```

<img src="Week 6.assets/Screen Shot 2023-05-25 at 10.18.34 PM.png" alt="Screen Shot 2023-05-25 at 10.18.34 PM" style="zoom:50%;" />

##### Common data structures

- Stacks
  - Last In, First Out
- Queues
  - First In, First Out

- Sets
  - Collection of ordered items
- Hash tables
  - Collection of unordered items

##### Stack ("naive" version)

```c
/* Naive lock-free stack push */
void stack_push(stack_t stack, void *data)
{
  struct node *n = malloc(sizeof(struct node));
  n->data = data;
  
  do {
    n->next = stack->top;
  } while (!cas(&stack->top, n->next, n));
}
// Fail for thread 2 because n->next no longer points to original stack top, instead, thread 1's data.
// In the second iteration, thread 2's data points to the new stack top, which is thread 1
// We are not using any lock in this stack push.
```

```c
/* Lock-based stack push
 * (for reference) */
void stack_push(stack_t stack, void *data)
{
  struct node *n = malloc(sizeof(*n));
  n->data = data;
  
  lock(&stack->lock);
  n->next = stack->top;
  stack->top = n;
  unlock(&stack->lock);
}
```

What about stack_pop()?

- See [link](https://www.singlestore.com/blog/common-pitfalls-in-writing-lock-free-algorithms/) for more details

#### Transactional memory

- No locks, just shared data
- Optimistic concurrency
  - Execute critical section speculatively, abort on conflict
- Software or hardware implementations

##### API

- `begin_transaction()`:
  - `Entering critical section`
  - Create checkpoint
  - Start tracking read set (remember memory addresses that are read)
  - See if anyone else is trying to write to these addresses
  - Locally buffer all the writes (invisible to other processors)
- `end_transaction()`:
  - Check read set: is data still valid? (i.e. no writes to any of them)
    - Yes: commit transaction => perform writes atomically
    - No: abort transaction => restore checkpoint

##### Example

```c
void perform_transfer(int from, int to, int amount)
{
  begin_transaction();
  if (accounts[from].balance >= amount) {
    accounts[from].balance -= amount;
    accounts[to].balance += amount;
}
  end_transaction();
}
```

```c
void thread_a(void)
{
  /* Transfer 1a */
  perform_transfer(1, 4, 100);
  ...
  /* Transfer 2a */
  perform_transfer(2, 5, 74);
}

```

```c
void thread_b(void)
{
	/* Transfer 1b */
	perform_transfer(3, 2, 3942);
	...
	/* Transfer 2b */
	perform_transfer(5, 2, 93);
}
```
