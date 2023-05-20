[toc]

# 5/15 L7.1 - Storage (1)

### Introduction

#### Memory and registers access

- By default, instructions have access to processor registers and to the memory (via memory addresses)

  ```assembly
  .section .data
  array: .word 1, 2, 3, 4, 5
  length: .word 5
  
  .section .text
  .globl main
  main:
      la $a0, array 				# int *a = @array
      lw $a1, length 				# int l = length
      li $t0, 0							# int i = 0
      
  loop:
      bge $t0, $a1, end 		# End when i == l
      
      lw $t1, 0($a0) 				# t1 = *a
      addi $t1, $t1, 1 			# t1 += 1
      sw $t1, 0($a0) 				# *a = t1
      
      addi $t0, $t0, 1 			# i++
      
      addi $a0, $a0, 4 			# a++
      
      j loop 								# Next iteration of loop
      
  end:
  		j $ra 								# Return to caller
  ```

#### Memory issues

- Volatile
  - Data lost upon power off
- Small
  - 8 GiB to 16 GiB of main memory on average for typical laptops
- Expensive
  - Few thousands dollars per GiB (CPU caches)
  - Five/ten dollars per GiB (main memory)

=> Need for big and cheap persistent storage!

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.35.05 AM.png" alt="Screen Shot 2023-05-16 at 2.35.05 AM" style="zoom:50%;" />

#### Memory hierarchy

- Size

  - From few MiB of CPU caches
  - To several TiBs of secondary storage

- Cost (secondary storage)

  - Dozen cents per GiB (local)
  - Few cents per GiB (remote)

- Speed

  - From nanoseconds for CPU caches
  - To seconds for remote secondary storage

  `we add secondary storage to expand the memory hierarchy`

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.37.38 AM.png" alt="Screen Shot 2023-05-16 at 2.37.38 AM" style="zoom:50%;" />

##### Other aspects

- Addressability 
  - `When cpu access main memory, cpu directly give the instructions. Cpu has to configure controller, more complicated`
- Byte vs block access
  - `We have to access an entire block in secondary storage.`
- Persistence
  - `We will have the data even power off.`
- Latency/throughput
- Power drain (in use/idle)
- Weight/volume
  - `Take more power and take more space.`

#### Secondary storage access

##### Block device

```c
void *base = 0xDEADBEEF;

int blk_read(uint32_t nblocks, uint32_t lba, uint32_t *addr)
{
    /* Make sure device is not busy already */
    if (readl(base + BLK_STATUS) & BLK_BUSY)
    return -1;
  
    /* Configure transfer */
    writel(base + BLK_NBLK) = nblocks;
    writel(base + BLK_BLKA) = lba;
    writel(base + BLK_MEMA) = addr;
  
    /* Start transfer */
    writel(base + BLK_CTRL) = BLK_IRQE;
  
    return 0;
}
```

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.43.44 AM.png" alt="Screen Shot 2023-05-16 at 2.43.44 AM" style="zoom:50%;" />

##### Cloud

```c
#include <google/cloud/storage/client.h>

int main(void)
{
    namespace gcs = ::google::cloud::storage;
  
    // Create a Google Cloud Storage client
    auto client = gcs::Client();
  
    // Read the contents of the object
    auto reader = client.ReadObject("bucket", "object");
  
    // Process the data
    std::string data{
    		std::istreambuf_iterator<char>{reader}, {}
    };
    printf("%s\n", data.c_str());
    ...
}
```

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.44.39 AM.png" alt="Screen Shot 2023-05-16 at 2.44.39 AM" style="zoom:50%;" />

### Technologies

#### Volatile memory

##### SRAM

*Static random-access memory*

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.46.14 AM.png" alt="Screen Shot 2023-05-16 at 2.46.14 AM" style="zoom:50%;" />

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.45.50 AM.png" alt="Screen Shot 2023-05-16 at 2.45.50 AM" style="zoom:50%;" />

###### Characteristics

- Bits stored in transistor flip/flops
- Bits degrade on poweroff

###### Performance

- Access time between 1 - 10 ns

###### Typical use

- On-chip cache

##### DRAM

*Dynamic random-access memory*

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.49.04 AM.png" alt="Screen Shot 2023-05-16 at 2.49.04 AM" style="zoom:50%;" />

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.47.25 AM.png" alt="Screen Shot 2023-05-16 at 2.47.25 AM" style="zoom:50%;" />

###### Characteristics

- Bits stored in capacitors
- 2D/3D array for dense packing
- Bits degrade even when powered: need to be periodically refreshed

###### Performance

- Access time between between 50 - 100 ns
- Transfer bandwidth up to 25GiB/s

###### Typical use

- Off-chip volatile memory

#### Persistent memory

##### Magnetic disk

 <img src="Week 7.assets/Screen Shot 2023-05-16 at 2.50.17 AM.png" alt="Screen Shot 2023-05-16 at 2.50.17 AM" style="zoom:50%;" />

`One direction represents 1 or 0. Opposite direction represents 0 or 1.`

###### Characteristics

- \> 1 Tbit per square inch
- Physical motion needed to read bits off surface
- Not directly addressable
- Block level random access

###### Performance

- 10ms random access latency
- Up to 200MiB/s streaming access

###### Typical use

- Desktops, data center bulk storage

##### Flash/SSD

*Solid State Drive*

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.54.40 AM.png" alt="Screen Shot 2023-05-16 at 2.54.40 AM" style="zoom:50%;" />

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.53.43 AM.png" alt="Screen Shot 2023-05-16 at 2.53.43 AM" style="zoom:50%;" />

###### Characteristics

- Blocks of bits stored persistently in silicon (even when unpowered)
- Densely packed in 2D array (newly 3D)
- Electrically reprogrammable (*for a limited number of times*)
  - Writes must be to a clean page, no update in place
  - Erasing only for regions of blocks (~256 KiB)

###### Performance

- 100μs random access latency
- 200MiB/s to +2000MiB/s

###### Typical use

- Smartphones, laptops, camera



### Magnetic disks - 机械硬盘

#### Anatomy

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.55.50 AM.png" alt="Screen Shot 2023-05-16 at 2.55.50 AM" style="zoom:50%;" />

#### History

- Principle hasn't really changed since the mid-1950s

<img src="Week 7.assets/Screen Shot 2023-05-16 at 2.59.46 AM.png" alt="Screen Shot 2023-05-16 at 2.59.46 AM" style="zoom:50%;" />

#### Disk operations

- When accessing a sector:
  1. Arm moves to correct cylinder, and proper head is enabled to reach the track containing the sector
     - *Seek time* (+ settle time)
     - `Time to find the track`
  2. Wait for sector to appear under head
     - *Rotation time*
     - `Disk to rotate enough. Exact position head is on`
  3. Read/write sector as it spins by
     - *Transfer time*
     - `Read and send back to the OS`
- *Access time = seek time + rotation time + transfer time*

<img src="Week 7.assets/Screen Shot 2023-05-16 at 3.01.54 AM.png" alt="Screen Shot 2023-05-16 at 3.01.54 AM" style="zoom:50%;" />

#### Disk performance

##### Seek time

- Time to position the head over a track
  - Depends on how fast the arm assembly moves the arms
- Head switch time (i.e. same cylinder, but different head/track)

<img src="Week 7.assets/Screen Shot 2023-05-16 at 3.06.36 AM.png" alt="Screen Shot 2023-05-16 at 3.06.36 AM" style="zoom:50%;" />

###### Characteristics

- Maximum seek time
  - From innermost track to outermost track
    - `From one end to the other`
  - ~10ms to 20ms
- Minimum seek time
  - From one track to the next one (even on same cylinder)
  - ~1ms
- Average seek time
  - Average between each possible pairs of tracks
  - 1/3 maximum time

##### Rotation time

<img src="Week 7.assets/Screen Shot 2023-05-16 at 3.11.41 AM.png" alt="Screen Shot 2023-05-16 at 3.11.41 AM" style="zoom:50%;" />

- Time for the sector to appear underneath the head
  - Depends on how fast the disk spins (e.g. 4200/5400/7200/10k/15k RPM)

###### Characteristics

- Rotation latency is typically half of full rotation
  - E.g., ~15ms (for 4,200 RPM disk) to 4ms (for 15,000 RPM disk)

##### Transfer Time

<img src="Week 7.assets/Screen Shot 2023-05-16 at 3.13.55 AM.png" alt="Screen Shot 2023-05-16 at 3.13.55 AM" style="zoom:50%;" />

- Time to move the bytes from disk to memory
- Surface transfer time (*from surface to disk buffer*)
- Host transfer time (*from disk buffer to main memory*)

###### Characteristics

- Uses intermediary disk buffer
- Surface time
  - Higher bandwidth for outer tracks (same rotation speed but longer)
  - Much smaller than seek or rotation times (512 bytes at 100 MiB/s = 5 μs)
- Host time
  - Depends on connection between disk and system
  - E.g., 35 MiB/s (USB-2) to 400 MiB/s (USB-3) to 600 MiB/s (SATA 3)

#### Example: Toshiba MK3254GSY (2009)

| Specifications        |              |
| --------------------- | ------------ |
| Platters/ Head        | 2/4          |
| Capacity              | 320 GiB      |
| Spindle speed         | 7200 RPM     |
| Average seek time R/W | 10.5/12 ms   |
| Track-to-track        | 1 ms         |
| Surface transfer time | 54-128 MiB/s |
| Host transfer time    | 375 MiB/s    |
| Buffer                | 16 MiB       |

`Write going to take longer since it needs to be exact location`

##### Example 1: 500 random reads

###### Description

- Workload
  - 500 read requests
  - Randomly chosen sectors
  - Served in FIFO order
- How long to service them?
  - seek time: 10.5 ms
  - rotation time: 4.15 ms
    - `7200RPM (Rotation per minutes) -> 120 Rotation per Seconds -> 8.3ms/rotation`
  - transfer time: at least 54 MiB/s
    - `9.5 μs to read 512 bytes sector`

###### Result

- Seek time: 10.5 ms
- Rotation time: 4.15 ms
  - 7200RPM => 120 RPS => 8.3 ms/rotation
- Transfer time: 9 μs
  - 512 bytes at 54 MiB/s
- 500 * (10.5 ms + 4.15 ms + 9 μs) = 7.3 s!

##### Example 2: 500 random reads

###### Description

- Workload
  - 500 read requests
  - Sequential sectors on same track
- How long to service them?
  - seek time: 10.5 ms
  - rotation time: 4.15 ms
  - transfer time: 54-128 MiB/s

`Same before, only read sequential sectors on same track`

###### Result

- Seek time: 10.5 ms

`Since it is sequential sectors, we only move the head once to seek.`

- Rotation time: 4.15 ms
  - 7200RPM => 120 RPS => 8.3 ms/rotation
- Transfer time:
  - outer track: 4 μs (512 bytes at 128 MiB/s)
  - inner track: 9 μs (512 bytes at 54 MiB/s)
- 10.5 + 4.15 + 500 * 4 μs = 16.65 ms
- 10.5 + 4.15 + 500 * 9 μs = 19.15 ms



# 5/17 L7.2 - Storage (2)

### Recap

#### Technologies

- Memory
  - SRAM
  - DRAM
- Secondary storage
  - Magnetic disk
  - Flash memory

### Magnetic disks

##### Anatomy

<img src="Week 7.assets/Screen Shot 2023-05-20 at 4.07.35 PM.png" alt="Screen Shot 2023-05-20 at 4.07.35 PM" style="zoom:50%;" />

##### Disk performance

- *Access time = seek time + rotation time + transfer time*

<img src="Week 7.assets/Screen Shot 2023-05-20 at 4.08.05 PM.png" alt="Screen Shot 2023-05-20 at 4.08.05 PM" style="zoom:50%;" />

#### Disk Scheduling

`Random reads will take much larger time than sequential reads`

##### Rationale

- Seek and rotation times dominate the cost of small accesses
- Disk transfer bandwidth is wasted
- Need algorithms to reduce seek time

##### Scheduling benchmark

- Queue of disk I/O requests

<img src="Week 7.assets/Screen Shot 2023-05-20 at 4.13.37 PM.png" alt="Screen Shot 2023-05-20 at 4.13.37 PM" style="zoom:50%;" />

- Objective: (re-)schedule requests to minimize seek time
- Metric: total head movement (in number of tracks)

#### Scheduling: FCFS

- *First come, first server* (aka FIFO)

<img src="Week 7.assets/Screen Shot 2023-05-20 at 4.17.16 PM.png" alt="Screen Shot 2023-05-20 at 4.17.16 PM" style="zoom:50%;" />

- Total head movement: 640 tracks

`Completely fair. Process with the order it arrived.`

`Flaw: wild swing, moves the head back and forth on the surface of the disk.`

#### Scheduling: SSTF

- *Shortest seek time first*

<img src="Week 7.assets/Screen Shot 2023-05-20 at 4.20.13 PM.png" alt="Screen Shot 2023-05-20 at 4.20.13 PM" style="zoom:50%;" />

- Total head movement: 236 tracks

`Always go to the nearest request. 1/3 of FIFO movements`

`One issue is You keep having the new clients in the area you're already in, and not moving anywhere else. Keep the farther position waiting. also called starvation.`

### Scheduling: SCAN

- The *elevator* algorithm

<img src="Week 7.assets/Screen Shot 2023-05-20 at 4.23.21 PM.png" alt="Screen Shot 2023-05-20 at 4.23.21 PM" style="zoom:50%;" />

- Total head movement: 208 tracks

`Kind of like elevator. Starts at 53 and arm will only go in one direction, serving all the request on the way to 0, and go all the way to the other end, serving all the request on the other way.`

`A little unfair, waste time going to request in the middle`

#### Scheduling: C-SCAN

- The *circular* elevator algorithm
  - Goes back directly to beginning after scanning

<img src="Week 7.assets/Screen Shot 2023-05-20 at 4.26.02 PM.png" alt="Screen Shot 2023-05-20 at 4.26.02 PM" style="zoom:50%;" />

- Total head movement: 183 tracks (+200 for return trip)

`Start from 53, only go in one direction (to the end). Once reach end, we go as fast as possible to the begining (return trip) and go to end again to proceed other requests.`

#### Scheduling: C-LOOK

- Optimized C-SCAN
  - Goes only as far as last request in each direction

<img src="Week 7.assets/Screen Shot 2023-05-20 at 4.29.13 PM.png" alt="Screen Shot 2023-05-20 at 4.29.13 PM" style="zoom:50%;" />

- Total head movement: 153 tracks (+169 for return trip)

