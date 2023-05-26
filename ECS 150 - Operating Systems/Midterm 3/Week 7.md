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

#### Scheduling: SCAN

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

`Return as soon as hit the last request (node) in this direction`

#### Scheduling

##### Other algorithms

- R-CSCAN
  - Account for rotation time
  - Allow small steps back and forth during scanning

- F-SCAN
  - Two I/O request queues to prevent arm "stickiness"
  - Service one queue, while new requests are enqueued in other queue
  - At the end of scan, swap queues
- N-SCAN
  - Same as F-SCAN but multiple queues

##### Summary

- FCFS
- SSTF
- Elevator algorithms (e.g., SCAN, C-SCAN, C-LOOK)

`None of them is the best, they are just good in different scenarios`

#### Effects of disk scheduling (C-LOOK)

| Specifications         |              |
| ---------------------- | ------------ |
| Platters/Heads         | 2/4          |
| Capacity               | 320 GiB      |
| Spindle speed 7200 RPM | 7200 RPM     |
| Average seek time R/W  | 10.5 / 12 ms |
| Track-to-track         | 1 ms         |
| Surface transfer time  | 54-128 MiB/s |
| Host transfer time     | 375 MiB/s    |
| Buffer                 | 16 MiB       |

##### Description

- Workload
  - 500 read requests
  - Randomly chosen sectors
  - Disk head on outside track
  - Served in **C-LOOK** order 
- How long to service them?
  - seek time: estimated as 1-track seek + 0.2% seek `1/500 request`
  - rotation time: 4.15 ms
  - transfer time: at least 54 MiB/s

##### Result

- Seek time: 1.06 ms

  - Estimated 0.2% seek: 1ms + (0.2/33.3) *10.5 ms

  - [Why is average disk seek time one-third of the full seek time?](https://stackoverflow.com/questions/9828736/why-is-average-disk-seek-time-one-third-of-the-full-seek-time)

  - `10.5 ms corresponds to 1/3 of the time travel the whole circle. Seek time is 0.2% of the whole circle`

- Rotation time: 4.15 ms

  - 7200RPM => 120 RPS => 8.3 ms/rotation

- Transfer time: 9 μs

  - 512 bytes at 54 MiB/s

- 500 * (1.06ms + 4.15ms + 9 μs) = **2.61 s**!

`Massive improvement than before. By reorganizing the queue.`

### Flash storage

#### Characteristics

<img src="Week 7.assets/Screen Shot 2023-05-21 at 9.26.43 PM.png" alt="Screen Shot 2023-05-21 at 9.26.43 PM" style="zoom:50%;" />

- No moving parts
- Better random access performance
- Less power
- More resistant to physical damage
- But also, more expensive...

##### Technologies

<img src="Week 7.assets/Screen Shot 2023-05-21 at 9.28.19 PM.png" alt="Screen Shot 2023-05-21 at 9.28.19 PM" style="zoom:50%;" />

- NOR vs **NAND**
- Single- vs Multi-level

#### Organization

<img src="Week 7.assets/Screen Shot 2023-05-21 at 9.34.55 PM.png" alt="Screen Shot 2023-05-21 at 9.34.55 PM" style="zoom:50%;" />

Typical sizes:

- Page: 4 KiB
- Block: 128 pages (512 KiB)
- Plane: 1024 blocks (512 MiB)

- Multiple independent data paths accessible in parallel

#### Operations

- Read and writes only occur in page units

- Read page: ~10 μs
- Write page: ~100 μs
  - Can only write an empty page (and not update existing page) `Can not overwrite the page already been use.`
  - But pages can only be emptied at block level `Only reset (erase) the page when you reset the entire block.`
- Erase block: > 1 ms

#### Page writing

- How long does it take to write to a single page?
- Example flash drive specifications
  - 4 KiB page
  - 3 ms block erasure time
  - 512 KiB block (128 pages)
  - 50 μs read/write page

<img src="Week 7.assets/Screen Shot 2023-05-21 at 9.39.13 PM.png" alt="Screen Shot 2023-05-21 at 9.39.13 PM" style="zoom:50%;" />

##### Naive approach

- Read block (except new page) `Read and save it to memory 127个 page`
- Erase block
- Rewrite block + new page `Rewrite the block with 新的 page and 127个page `
- $Total = 127 * 50\mu s+3ms+128*50\mu s = 16ms$

`1. save the entire block 2. erase the whole block 3. write the data into block`

##### Smarter approach

<img src="Week 7.assets/Screen Shot 2023-05-21 at 9.42.33 PM.png" alt="Screen Shot 2023-05-21 at 9.42.33 PM" style="zoom:50%;" />

- *Flash translation layer*
  - Map logical pages to physical pages
  - `We map one new empty block for write data, and map the logic if we need to write instead of erase new block.`
- Make free erased block(s)
- Cost of erasure is amortized
- $Total = (3ms/128) + 50μs = 73.4μs$

#### Durability

##### Wear out

Flash memory stops reliably storing a bit

- After many erasures (on the order of 10^3 to 10^6) `It will stop reading after many read` 
- After nearby cells are read many times (read disturb) `Read might be disturb`

##### Solutions

- Error correcting codes `Detect some bits would lost and recover them`
- Wear leveling `Keep moving pages so that no single block got too tired`
  - Using write remapping
- Bad pages/erasure blocks `Mark the bad page and not using them.`
- Spare pages and erasure blocks `Have some extra page to mitigate if there are bad pages`

#### Example: Intel 710 series SSD (2011)

| Specifications         |                        |
| ---------------------- | ---------------------- |
| Capacity               | 320 GiB                |
| Page size              | 4 KB                   |
| Bandwidth (seq reads)  | 270 MiB/s              |
| Bandwidth (seq writes) | 210 MiB/s              |
| R/W latency            | 75 μs                  |
| Random reads/s         | 38,500 (ie 26 μs/read) |
| Random writes/s        | 2,000                  |

##### Description

- Workload
  - 500 read requests
  - Randomly chosen sectors
- How long to service them?

##### Result

- 500 * 26 μs = 13 ms
  - (compared to 7.3 s for magnetic disk...!) `So much faster than magnetic disk`
  - `We don't really need scheduling for the SSD`



# 5/19 L7.3 - FS abstraction

### Introduction

#### Long-term data storage requirements

<img src="Week 7.assets/Screen Shot 2023-05-22 at 9.16.29 PM.png" alt="Screen Shot 2023-05-22 at 9.16.29 PM" style="zoom:50%;" />

- (Very) large amounts of non-volatile data `How to handle these TB of data`
- Easy way to find data
- Concurrent access from processes
- Controlled sharing between users
- Performance
- Reliability
  - Survive power-off, OS crash, process termination

#### Filesystems (FS)

<img src="Week 7.assets/Screen Shot 2023-05-22 at 9.17.02 PM.png" alt="Screen Shot 2023-05-22 at 9.17.02 PM" style="zoom:50%;" />

Abstractions for maximizing the usage of non-volatile storage

- Persistent, named data (files)
- Hierarchical organization (directories, subdirectories)
- Performance
- Access control
- Crash and storage error tolerance
  

### Filesystem concepts

#### File

- Abstraction that provides persistent, described data
- Logical storage unit

<img src="Week 7.assets/Screen Shot 2023-05-22 at 9.23.14 PM.png" alt="Screen Shot 2023-05-22 at 9.23.14 PM" style="zoom:50%;" />

##### Metadata

- File attributes added and managed by the OS
- Size, creation/modification time, owner, permissions, etc.
- File's content location (index structure)

##### Data

- What a user puts in the file
- Array of untyped bytes

`File doesn't know its own name`

#### Directory

Directories provide names for files

- Groups of named files or subdirectories
- Mapping from human-readable file names to file metadata locations

<img src="Week 7.assets/Screen Shot 2023-05-22 at 9.26.20 PM.png" alt="Screen Shot 2023-05-22 at 9.26.20 PM" style="zoom:50%;" />

#### Naming conventions

- Depends on filesystem `FAT Filesystem, FFS Filesystem, NTFS...`

##### Case

- Windows traditionally case-insensitive
- UNIX traditionally case-sensitive

##### Length

- Before, could be quite limited (11 with MS-FAT)
- Today, usually up to 255 characters

##### Extension

- Before, separated from filename, and meaningful `.txt, .mp3`
- Today, usually part of the filename, and just a hint

```bash
$ file innocent_text_file.txt
innocent_text_file: ELF 64-bit LSB executable, x86-64,
version 1 (SYSV), dynamically linked

$ file scary_executable_file.x
scary_executable_file.x: ASCII text
```

`Executable and scripts starts with magic number and ELF`

`OS is going to read and call sh on that script`

#### Path

##### Absolute

- Path of file from the root directory
  - e.g., `vim /home/jporquet/project3/secret-solution/fs.c`

##### Relative

- Path from the current working directory (`CWD`)
- `CWD` stored in process' PCB
  - e.g., `cd /home/jporquet && vim project3/secret-solution/fs.c`

##### Special entries

- 2 special entries in each UNIX directory
  - `.`: current directory
  - `..`: parent directory

#### Hard link

Link from name to metadata location

<img src="Week 7.assets/Screen Shot 2023-05-22 at 9.37.52 PM.png" alt="Screen Shot 2023-05-22 at 9.37.52 PM" style="zoom:50%;" />

`Instead of tree, it becomes a graph (directed graph)`

##### Hard links and cycles

- No difference between an "original" file name and a hard-link to it

  ```bash
  $ touch test
  $ ls -li test
  21892004 -rw-r--r-- 1 joel joel 0 2017-03-02 17:15 test
  
  $ ln test test2
  $ ls -li test*
  21892004 -rw-r--r-- 2 joel joel 0 2017-03-02 17:15 test
  21892004 -rw-r--r-- 2 joel joel 0 2017-03-02 17:15 test2
  # Their file number is identical!
  # I'm having two mapping
  ```

- Creation of a directory loop would make any file tree walker error-prone
  (e.g. `du` or `fsck`)

  - Unless keeping track of all the inodes that are being traversed

- Hard-links to directory are forbidden

  ```bash
  $ mkdir mydir
  $ ln mydir mydirlink
  ln: mydir: hard link not allowed for directory
  ```

Solution: special type of files dedicated for links.

`There can't be cycles. Directed Graph but ascyclic! (DAG)`

#### Soft link (aka symbolic link)

Link from name to alternate name

<img src="Week 7.assets/Screen Shot 2023-05-22 at 9.46.28 PM.png" alt="Screen Shot 2023-05-22 at 9.46.28 PM" style="zoom:50%;" />

- Symlinks can be well-identified
- File tree walkers can safely ignore them 
  - `Can be detected by OS`
- POSIX limit: `#define _POSIX_SYMLOOP_MAX 8`

#### Volume

A volume is a collection of physical storage resources that form a logical storage device containing a file-system.
A volume can be:

- (a) A whole disk
- (b) A partition on a disk
- (c) Multiple disks (seen as one)

<img src="Week 7.assets/Screen Shot 2023-05-22 at 9.49.10 PM.png" alt="Screen Shot 2023-05-22 at 9.49.10 PM" style="zoom:50%;" />

#### Disk partitions

| MBR (old)                                                    | GPT (new)                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <img src="Week 7.assets/Screen Shot 2023-05-22 at 10.00.44 PM.png" alt="Screen Shot 2023-05-22 at 10.00.44 PM" style="zoom:50%;" /> | <img src="Week 7.assets/Screen Shot 2023-05-22 at 10.01.12 PM.png" alt="Screen Shot 2023-05-22 at 10.01.12 PM" style="zoom:50%;" /> |

#### Drive letter assignment (Windows)

- Each volume holds a fully independent tree, in its own namespace
- Letter assignment typical order:
  - `A:`, `B:`: first and second floppy disk drives
  - `C:`: first active primary partition of the first physical hard drive
  - Then, first active primary partition of subsequent physical hard drives, etc.

<img src="Week 7.assets/Screen Shot 2023-05-22 at 10.04.52 PM.png" alt="Screen Shot 2023-05-22 at 10.04.52 PM" style="zoom:50%;" />

#### Mounting (UNIX)

<img src="Week 7.assets/Screen Shot 2023-05-22 at 10.05.20 PM.png" alt="Screen Shot 2023-05-22 at 10.05.20 PM" style="zoom:50%;" />

`CSIF - boot and root partition (HDD partition 1 and 2)`

<img src="Week 7.assets/Screen Shot 2023-05-22 at 10.05.31 PM.png" alt="Screen Shot 2023-05-22 at 10.05.31 PM" style="zoom:50%;" />

`All of the partition put in the VFS `

`Root mounted to /, boot mounted to /boot, internet esrver mounted to /home`

Mounting multiple volumes arbitrarily in a single logical hierarchy

```bash
$ cat /etc/fstab 			# configuration file
/dev/sda1 				/ 						ext3			# first partition of first hard drive and its mounting point
/dev/sda2 				/boot 				ext3			# second partition of first hard drive
/dev/sdb1 				/home 				ext3			# first partition of second hard drive
/dev/cdrom 				/mnt/cdrom 		iso9660
/dev/sdc1 				/mnt/usbkey 	vfat
10.0.0.1:/shared 	/srv/shared 	nfs

# the following entry would not make much sense (duplicate) but is possible
#/dev/sdb1 				/mnt/home 		ext3
```

### Filesystem software layers

<img src="Week 7.assets/Screen Shot 2023-05-23 at 3.14.22 PM.png" alt="Screen Shot 2023-05-23 at 3.14.22 PM" style="zoom:50%;" />

### Filesystem typical API

#### Create and delete files

- `create(filename, mode)`
  - create new (empty) file, including metadata and name in directory
- `link(existing_filename, new_filename)`
  - create new name for same underlying file as existing filename
- `unlink(filename)`
  - remove name for a file from its directory

```c
int fd = open("test", O_CREAT, 0644); // also creat() but deprecated
close(fd);
link("test", "test2");
unlink("test");
```

```bash
$ ls -l 				# After open()+link()
-rw-r--r-- 2 joel joel 			0 2018-05-22 11:28 test
-rw-r--r-- 2 joel joel 			0 2018-05-22 11:28 test2
$ ls -l # After end of program
-rw-r--r-- 1 joel joel 			0 2018-05-22 11:44 test2
```

#### Open and close

- `open(filename)`
  - prepare to access specified file and return file descriptor
- `close(filename)`
  - release resources associated with open file

`different scenario in case of opening files.`

<img src="Week 7.assets/Screen Shot 2023-05-23 at 3.22.09 PM.png" alt="Screen Shot 2023-05-23 at 3.22.09 PM" style="zoom:50%;" />

#### File access

##### Sequential

- `read()`, `write()`: sequential reading/writing
- `seek()`: change file's current position for random access

```c
fd = open(...)
lseek(fd, 42, SEEK_CUR)
/* Write in open file at offset 42 */
char *c = 'a';
write(fd, &c, 1);
close(fd);
```

##### Random

- `mmap()`: create mapping between file's content and memory
- `munmap()`: destroy mapping

```c
fd = open(...)
char *address = mmap(0, len, PROT_WRITE, MAP_SHARED, fd, 0)
address[42] = 'a';
munmap(address, len);
close(fd);
```





