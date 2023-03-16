## [Start CPU Design (Lecture 02-27-2023)](https://video.ucdavis.edu/media/ECS154ALecture02-27-2023/1_iiui8sgy)

### CPU Design

##### CPU Instructions

- When building a CPU, the first thing you should think about is what you want your CPU to be able to do

  - Do you want it to be able to add, compare values, read values from memory etc?

- What you want your CPU to do will dictate what you will have to build

- There are two major parts that you have to build

  - The data path: This is all about making sure you are able to generate the right values and get them to right places. Do this first.
  - The control path: This is about choosing what values go where when. Do this second.

  

##### CPU Instruction Sizes

- Fixed length

  - All instructions are the same size
  - Simplifies instruction decoding and architecture
  - Some instructions waste space as they don’t need it all

- Variable length

  - Instructions vary in size. 
  - Instructions have exactly enough bits to describe what they do
  - Makes decoding and architecture more complicated

- Hybrid length

  - Instructions are multiples of some fixed size
  - Middle ground between the other two

  

##### CISC vs RISC

- CISC
  - Complex Instruction Set
  - Many different instructions that can accomplish all kinds of tasks
  - The thought process is that since hardware is fast, we should provide instructions to do as much as we can in it so that programmers can take advantage of them
  - Providing these complex instructions can increase the worst case path which slows down all instructions
- RISC
  - Reduced Instruction Set
  - Only provide the most commonly used instructions
  - The thought here is that most programmers (especially the compiler) won’t take advantage of all the specialized instructions, so instead focus on making the instructions and CPU as fast as possible (Make the common case fast)



##### Regularity

- Regularity in how instructions are structured is incredibly important as the more regular the structure the easier it will be to decode the instruction
- This regularity is challenged by the fact that different instructions need different pieces of information to do their job or operate in vastly different ways than other instructions (add vs jump)
- Because of the above most CPUs have a few different types of instruction formats to accommodate all of the different operations while still maintaining some consistency



##### MIPS

- A MIPS CPU 

  - Implements a RISC based architecture

  - Has a fixed size instruction format

    - 32 bits for a 32 bit CPU and 64 bits for a 64 bit CPU

  - Has 3 major instruction formats 

    - R-Type
    - I-Type
    - J-Type

    

##### R-Type Instructions

- **R**egister instruction
- Effect rd = rs funct rt
- Op for R type instructions is always 0
- Funct has the bits of the instruction to perform
- Shamt is shift amount and is only used by shift instructions

| Field | Op      | rs      | rt      | rd      | shamt  | funct |
| ----- | ------- | ------- | ------- | ------- | ------ | ----- |
| Size  | 6       | 5       | 5       | 5       | 5      | 6     |
| Bits  | 31 - 26 | 25 - 21 | 20 - 16 | 15 - 11 | 10 - 6 | 5 - 0 |



##### I-Type Instructions

- **I**mmediate instruction
- Effect: rt = rs op Sign Extended Immediate
- Sign extending the immediate means prepending the most significant bit of the immediate to the immediate until it is 32 bits big

| Field | op      | rs      | rt      | Immediate |
| ----- | ------- | ------- | ------- | --------- |
| Size  | 6       | 5       | 5       | 16        |
| Bits  | 31 - 26 | 25 - 21 | 20 - 16 | 15 - 0    |



##### J-Type Instructions

- **J**ump instruction
- Effect: $PC = (PC+4)_{31:28}:addr:00$
- : means concatenate

| Field | Op      | addr   |
| ----- | ------- | ------ |
| Size  | 6       | 26     |
| Bits  | 31 - 26 | 25 - 0 |



##### Major CPU Components

- Program Counter (PC): Stores the address of the instruction we are working on
- Register File: Small amount of memory inside of the CPU. Stores values we are working on
- ALU: Does the computations
- Decoder: Reads instruction and sets the control signals inside of the CPU to make it do what the instruction says

 

## [Single Cycle MIPS CPU (Discussion 02-27-2023)](https://video.ucdavis.edu/media/ECS154ADiscussion02-27-2023/1_aw4eaozj)

<img src="CPU-Design.assets/Screen Shot 2023-03-10 at 3.55.07 PM.png" alt="Screen Shot 2023-03-10 at 3.55.07 PM" style="zoom:50%;" />

PC is the program counter

###### Register File:

A1 and A2 is the register file you want to read.

`A1 = 5, RD1 would output the value of REG 5.`

A3 is the value that you want to write to (destination register)

`A3 is 9, WD3 is 12. Then register9 number 12 would be stored.` (Only if write signal is enabled.`WE3 = 1`)

How memory work? - You give it an address, it gives you the value at the address.

###### Data memory

If you give it address, it will read the value in this address if we = 0 (RD outputs the value). Write the value in WD to the address if WE = 1 (don't care the RD).

###### Example: load word (lw) instruction

`$t = MEM[$s + i]`

`immediate is bits 15-0, $t is bits 20-16, $s is bits 25-21`

1. Get bottom 16 bits of instruction (immediate), Sign extend it to 32 bits.

2. Plug `$s`, bits 25 - 21 into A1 in Register File, RD1 outputs the register value.

3. Add the immediate and `$s` together and put into A in Data Memory (using ALU). Set WE = 0 (since we are not writing) and output to RD.

4. We read the RD value (data at the address `MEM[$s + i]` and connect it back to WD3. 

5. Then we put bits 20:16 of the instruction bits `$t` into A3 and set the WE3 to 1. `lw` done.

   <img src="CPU-Design.assets/Screen Shot 2023-03-13 at 6.44.47 PM.png" alt="Screen Shot 2023-03-13 at 6.44.47 PM" style="zoom:50%;" />

6. Add 4 into program counter so we can go to the next instruction.

###### Example: store word (sw) instruction

`MEM [$s + i] = $t`

`immediate is bits 15-0, $t is bits 20-16, $s is bits 25-21`

Same as `lw` except

- Put `$t` into A2 and we get the value out from RD2

- Hook RD2 to WD and WE = 1 (write)

  <img src="CPU-Design.assets/Screen Shot 2023-03-13 at 6.43.25 PM.png" alt="Screen Shot 2023-03-13 at 6.43.25 PM" style="zoom:50%;" />

###### beq (branch if equal) instruction

`if ($s == $t) pc += i << 2`

subtract `$s` and `$t` and see if it is 0, check if the result is 0.

###### Build control path

<img src="CPU-Design.assets/Screen Shot 2023-03-13 at 7.07.35 PM.png" alt="Screen Shot 2023-03-13 at 7.07.35 PM" style="zoom:50%;" />

Combinational Logic

###### Jump Instruction

`pc += i<<2`

$PC = (PC+4)_{31:28}:addr:00$

`Op is bits 31-26, addr is bits 25 - 0`

<img src="CPU-Design.assets/Screen Shot 2023-03-13 at 7.11.21 PM.png" alt="Screen Shot 2023-03-13 at 7.11.21 PM" style="zoom:50%;" />



## [Multicycle CPU (Lecture 03-01-2023)](https://video.ucdavis.edu/media/ECS154Lecture03-01-2023/1_z7yi67vh)

- Single Cycle CPU, everybody have to go to your slowest instruction.
- Break our CPU down to take multiple steps, to make our CPU faster.
  - Some steps don't require read from memory can skip the read memory steps.

- We're going to store the instruction.

<img src="CPU-Design.assets/Screen Shot 2023-03-13 at 7.23.56 PM.png" alt="Screen Shot 2023-03-13 at 7.23.56 PM" style="zoom:50%;" />



`lw: $t = MEM[$s + i]`



## [Review CPU Design, Finish Multicycle CPU (Lecture 03-03-2023)](https://video.ucdavis.edu/media/ECS154ALecture03-03-2023/1_tbk0g5gn)

......



## [CPU Time, Caching (Lecture 03-06-2023)](https://video.ucdavis.edu/media/ECS154ALecture03-06-2023/1_gw7ko2d5)

##### Program Speed

- Number Of Instructions * (Clock Cycles / Instruction) * (Time / Clock Cycles)
  - Based on the equation we have, it really depends on the exact numbers involved.
    - In single cycle cpu, we have 1 clock cycle per instruction but huge time per clock cycles.
    - In multicycle cpu, we may have multiple clock cycle per instruction but smaller time per clock cycle.
      - <img src="CPU-Design.assets/Screen Shot 2023-03-14 at 5.16.50 AM.png" alt="Screen Shot 2023-03-14 at 5.16.50 AM" style="zoom:50%;" />
    - The longest time is ALU time 
- The above equation is for CPU time ONLY. `You might spend sometimes read from memory`



### Caching 缓存

##### Memory Technologies

- When building a CPU there are many different technologies that we can use to store information 
- Each of these technologies have different tradeoffs in terms of cost, speed, energy usage, and data density 
- Ideally we would like memory that is large, fast, and cheap but no single technology fits that bill 
  - We can get that behavior by making use of all of the technologies and caching more frequently used values in the faster memory

From most expensive, fastest, least energy efficient, least dense to cheapest, slowest, most energy efficient, and most dense

- Registers
- SRAM
- DRAM
- SSD
- HDD

<img src="CPU-Design.assets/bCPt6Hh0DM8dzn1IX05SGWsC5mkEDM83mqK63BuUBbkCOZvo9H-Px1lBugE64xsGJoJKFXA-X2GaD6AznxfhZDONtV7PZD-BEruSyb285mEcHrL3R4BCoAbeP7iBUBnK2IHSzTE2BenLuFNgI8BOLp9C5ndRT5vj1Po4N46gEKje-a9B0oGJAAqxRSgf52s=s2048.png" alt="img" style="zoom:50%;" />



##### What is a Cache?

- A cache is a smaller, faster memory component inserted between main memory and the CPU that stores values recently accessed in memory

  `smaller than RAM and faster than RAM`

- To the CPU the Cache is invisible 

- Requests to memory are intercepted by the cache and if the cache has the desired value it is given to the CPU without accessing main memory. If the value is missing the cache retrieves the value from memory and stores it inside itself and then gives the value to the CPU

- The idea of a Cache can be extend beyond the cache itself
  - Main memory serves as a cache for the hard drive
  - Registers serve as a cache for the cache
  
- Caching is also used in the internet
  - Servers will cache web pages that are accessed and serve up the cached pages instead of having to go to the primary server every time
  - Saves time and reduces network traffic 

`Having food in refrigerator (cache), if no, go to grocery store (main memory)`



##### Why Caching Works

- Caching works because programs exhibit spatial and temporal locality.
- Spatial Locality
  - Values next to each other are often accessed together 
  - Due to arrays and functions 
    - Functions because local variables and arguments are near each other
- Temporal Locality
  - A value recently accessed is likely to be accessed again
  - Due to functions
    - You keep accessing the local variables and arguments of a function over and over again inside the function
- If memory accesses were random caching would have no effect

`Because we are not access memory randomly. (Predict)`



##### Some Terminology 

- Given that a cache is smaller than the whole it stores the cache will not always contain the value we are looking for
- A Hit happens when we look inside the Cache and find the thing we are looking for
- A Miss is when we look inside the Cache and do not find the thing we are looking for

`Cache not always going to find what we are looking for`

`Hit/Miss`



##### Cache Performance

- Hit Rate = number of hits / number of cache accesses
- Miss Rate = number of misses / number of cache accesses
- Hit Rate + Miss Rate = 1
- Cache Access Time: time it takes to find something in the cache or find it isn’t there

`amount of time searching`

- Memory Access Time: the amount of time it takes to retrieve a value from memory

`time of driving to grocery store and back `

- Average Memory access time = Cache Access Time + (Memory Access Time * Miss Rate)	`Single cache access time`
  - We always have to look in the cache which is why we have to pay the Cache Access time but only have to access memory when we miss

###### Example

| Fridge Access Time | 10 seconds | 0.16666... minutes |
| ------------------ | ---------- | ------------------ |
| Grocery Store Trip | 20 minutes |                    |
| Miss Rate          | 30.00%     |                    |

When your friend asks for food. How long on **average** will it take to feed them?

- 0.1666... minutes + 20 minutes * 0.3 = 6.6666.. minutes

<img src="CPU-Design.assets/Screen Shot 2023-03-14 at 6.11.45 AM.png" alt="Screen Shot 2023-03-14 at 6.11.45 AM" style="zoom:50%;" />



## [Caching: Mapping Strategies (Discussion 03-06-2023)](https://video.ucdavis.edu/media/ECS154ADiscussion03-06-2023/1_9uvbw4np)

##### Multilevel Caching

- We can have caches for our caches to help improve our performance
- CA1 is the time to access cache 1
- MR1 is the miss rate for for cache 1
- CA2 is the time to access cache 2
- MR2 is the miss rate for for cache 2
- MA is access time for memory
- Then the average memory access time is $CA1 + MR1 *(CA2 + MR2* MA)$

Three-level cache: $CA1 + MR1 *(CA2 + MR2*(CA3+MR3*MA))$

 [CPU-Design_3-6.xlsx](CPU-Design_3-6.xlsx) 



## [Caching: Block Size, Address Partitioning (Lecture 03-08-2023)](https://video.ucdavis.edu/media/ECS154ALecture03-08-2023/1_13052lrc)

`Three ways of cache: Fully associative, Directly Mapped, K-way Associative`

`Spatial Locality Cache`

##### More Cache Terms

- Word: An element in memory
  - The unit of transfer between the cache and the CPU
- Block: A contiguous chunk(next to each other) of words in memory
  - Essentially an array
  - The unit of transfer between memory and the cache
- Way: Is a block inside of a set along with the additional overhead bits related to a block to make the cache work
- Set: a collection of Ways
- Cache: is a collection of Sets



- C = **data** capacity of the cache in terms of words
- b = the block size
- B = the number of Blocks
  - B = C / b
- N = the degree of associativity. AKA number of Ways per Set
  - Number of ways == Number of Blocks per Set
- S = the number of sets in the Cache
  - S = B / N
- A = the address of memory being accessed



##### Important Equations

With powers of 2 (for Set and block size)
- Number of offset bits = $log_2(b)$
- Number of set bits = $log_2(S)$
- Number of tag bits = number of address bits - number of set bits - number of offset bits

| Address |      |        |
| ------- | ---- | ------ |
| Tag     | Set  | Offset |



With non-powers of two

- The set that has element with address A is floor(A/b) % S
- The element will appear at A % b in the line
- Have to store the entire address as tag

```python
a = 0b1011011101
x = a % 2**3
bin(x) -> 0b101
bin(a & 0b111) -> 0b101
```



###### Examples - K Way Set Associative with K = 5

| Parameter        | Size       |                    |
| ---------------- | ---------- | ------------------ |
| Cache Data       | 5120 bytes | $5*2^{10}$  = 5120 |
| Block Size       | 4 bytes    |                    |
| Number of Ways   | 5          |                    |
| Number of Blocks | 1280       | 5120/4 = 1280      |
| Number of Sets   | 256        | 1280/5 = 256       |
| Address Size     | 32 bits    |                    |

Address Size:
| TAG              | Set                        | Offset                |
| ---------------- | -------------------------- | --------------------- |
| 32-8-2 = 22 bits | log2(sets) = log2(256) = 8 | log2(b) = log2(4) = 2 |



## [Caching: Eviction Policies (Lecture 03-10-2023)](https://video.ucdavis.edu/media/ECS154ALecture03-10-2023/1_y0mqwf6n)

###### Example - Fully Associative

| Parameter        | Size       |                                                  |
| ---------------- | ---------- | ------------------------------------------------ |
| Cache Data       | 4096 bytes |                                                  |
| Block Size       | 8 bytes    |                                                  |
| Number of Ways   | 512        | 4096/8 = 512                                     |
| Number of Blocks | 512        | 4096/8 = 512                                     |
| Number of Sets   | 1          | K always = 1 since it is fully associative cache |
| Address Size     | 20 bits    |                                                  |

Address Size:

| TAG     | Set               | Offset           |
| ------- | ----------------- | ---------------- |
| 17 bits | log2 (1) = 0 bits | log2(8) = 3 bits |



###### Example - Direct Mapped Cache

| Parameter        | Size       |                                             |
| ---------------- | ---------- | ------------------------------------------- |
| Cache Data       | 4096 bytes |                                             |
| Block Size       | 8 bytes    |                                             |
| Number of Ways   | 1          | In direct mapped, number of ways always = 1 |
| Number of Blocks | 512        | 4096/8 = 512                                |
| Number of Sets   | 512        | 512/1 = 512                                 |
| Address Size     | 20 bits    |                                             |

Address Size:

| TAG             | Set                 | Offset           |
| --------------- | ------------------- | ---------------- |
| 20-9-3 = 8 bits | log2 (512) = 9 bits | log2(8) = 3 bits |



##### Mapping Strategies

1. Direct Mapped Cache
   - Number of Ways = 1
2. Fully Associative Cache
   - Number of Sets = 1

3. K Way Set Associative

   - Number of Ways = K 

   

##### Eviction Policies

- Since the Cache is smaller than Memory, eventually we will run out of space in it and have to kick something out to make room for something new. If we are using a mapping strategy other than Direct Mapping we have to figure out who to kick out of a **Set**.
- First In First Out
- Least Recently Used 
- Most Recently Used
- Random



##### First In First Out/Round Robin
- Take turns kicking out the ways within a set 
- If we had 4 ways inside of a set kick out way 0,1,2,3,0,1,2,3, …
- Easy to implement. Just keep a single counter **per set**. The counter tells you which way to kick out. When a way **within the set** gets kicked out, increment the counter for **that set**. 

###### Example

| Set  | Counter  | 3         |           |      |
| ---- | -------- | --------- | --------- | ---- |
| Ways | 0        | 1         | 2         | 3    |
| Tags | ~~5~~ 99 | ~~70~~ 50 | ~~20~~ 12 | 30   |



|      |      | Evict? | Who Would We Evict?       |
| ---- | ---- | ------ | ------------------------- |
| Read | 5    | No     |                           |
| Read | 70   | No     |                           |
| Read | 20   | No     |                           |
| Read | 30   | No     |                           |
| Read | 99   | Yes    | entry at position 0 (5)   |
| Read | 50   | Yes    | entry at position 1 (70)  |
| Read | 99   | No     | HIT (Match at position 0) |
| Read | 12   | Yes    | entry at position 2 (20)  |

