[toc]

# 5/22 L8.1 - FS implementation (1)

###  Introduction

#### Concepts

##### Volume

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.25.52 PM.png" alt="Screen Shot 2023-05-24 at 1.25.52 PM" style="zoom:50%;" />

- Disk, or partition on a disk
- Large array of sectors/blocks

##### Filesystem

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.26.51 PM.png" alt="Screen Shot 2023-05-24 at 1.26.51 PM" style="zoom:50%;" />

- Methods and data structures to organize files on a volume

##### Directory

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.27.06 PM.png" alt="Screen Shot 2023-05-24 at 1.27.06 PM" style="zoom:50%;" />

- Hierarchy of named files

##### File

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.26.15 PM.png" alt="Screen Shot 2023-05-24 at 1.26.15 PM" style="zoom:50%;" />

- Metadata to describe file's characteristics
- Actual sequence of data

### Design considerations

#### Workload (1)

##### File size and storage space

- Most files are small...

```bash
$ du -sh /usr/bin/
1.1G 	/usr/bin/		# on avg, 35KiB per file
$ ls -1 /usr/bin/ | wc -l
4566
```

- But large files account for more storage

```bash
$ du -h VirtualBox/Machines/Ubuntu/Ubuntu.vdi
20G 	VirtualBox/Machines/Ubuntu/Ubuntu.vdi	# one file takes 20G
```

`Most file are pretty small, but large file takes a lot of space`

##### File access and I/O transfer

- Most accesses are to small files...

```bash
$ strace chrome |& grep "open" | wc -l
557		# Chrome perform 557 syscalls to files, but it only takes one second
```

- But accesses to large file account for more I/O transfer

```bash
$ dd if=Ubuntu.vdi of=copy.vdi bs=4K
...
20981043200 bytes (21 GB, 20 GiB) copied, 62.74 s, 334 MB/s
# only copy Ubutn takes 1 minute
```

#### Workload (2)

##### File access pattern

- Most files are read/written sequentially
  - E.g., config files, executables
- Some files are read/written randomly
  - E.g., database files, swap files

##### File size growth

- Some files have pre-defined size at creation (e.g., downloaded files)
- Some files start small and grow over time (e.g., system logs)

##### File lifetime

- Some files always exist (e.g., OS files)
- Some files exist just for an instant (e.g., intermediary compilation file)

#### Blocks vs disk sectors

##### Rationale

- A disk sector is typically 512-bytes long
- But OS can allocate blocks of disk sectors rather than individual sectors
  - Doesn't cost much more to access few consecutive disk sectors

##### From small block size...

- E.g., size of single sector (512 bytes)
- Management requires more space
- More separate accesses to data
- Less wasted space if block not full

##### ...to big block size

- E.g., 32 KiB (64 consecutive sectors)
- Management requires less space
- Performance improvement
- Wasted space if block not full

##### Trade-off

- Make block size multiple of memory page size
- That is 4KiB on most systems

#### Review

##### Small files

- Small blocks for efficient storage
- Files used together should be stored together

##### Large files

- Large blocks for efficient storage
- Contiguous data allocation for sequentially accessed files
- Efficient lookup for randomly accessed files

##### Problems

- May not know at file creation
  - Whether file will become small or large
  - Whether file will be used sequentially or randomly
  - Whether file is persistent or temporary

#### Implementation overview

- From pair `<filename, offset>`, find physical storage block *efficiently*

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.43.52 PM.png" alt="Screen Shot 2023-05-24 at 1.43.52 PM" style="zoom:40%;" />

##### Data structures

- Directories
  - Map filenames to file numbers (to find metadata)
- Index structure
  - Part of a file's metadata
  - Map data blocks of file
- Free space map
  - Manage the list of free disk blocks
  - Allow files to grow/shrink

##### Data structures organization

- Storage devices often have non-uniform performance
- Use of *locality heuristics* to optimize data placement
  - Defragmentation
  - Files grouping

### Directories

#### Design

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.46.42 PM.png" alt="Screen Shot 2023-05-24 at 1.46.42 PM" style="zoom:50%;" />

- A directory is simply a **file**
  - List of mappings from filenames to file numbers
  - Each mapping is a directory entry
    - `<name, file number>`
- Only directly accessible by OS
  - Ensure integrity of mapping
  - Accessible for processes via syscalls
    - e.g., `opendir()/readdir()`

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.46.53 PM.png" alt="Screen Shot 2023-05-24 at 1.46.53 PM" style="zoom:50%;" />

Hexdump of directory (Project #3)

#### Organization strategies

##### Flat hierarchy

- One unique namespace for the entire volume
  - Use special area of the disk to hold root directory
  - Two files can never be named the same

`Only one directory. All of the files in there.`

##### Multi-user flat hierarchy

- Separate root directory for each user
- But all user's files must still have unique names...

`Still only have one directory for all of files`

##### Multi-user, multi-level hierarchy

- One special root directory
- Hierarchy of subdirectories
- Permissions to distinguish between users

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.50.26 PM.png" alt="Screen Shot 2023-05-24 at 1.50.26 PM" style="zoom:50%;" />

#### Implementation

##### Linear layout

- Simple array of entries
- E.g., MS-FAT, ECS150FS

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.50.53 PM.png" alt="Screen Shot 2023-05-24 at 1.50.53 PM" style="zoom:50%;" />

###### Pros/Cons

- Simple
- Need to scan all possible entries to find one

##### List layout

- Linked-list of entries
- E.g., ext2

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.52.14 PM.png" alt="Screen Shot 2023-05-24 at 1.52.14 PM" style="zoom:50%;" />

###### Pros/Cons

- Jump over blocks of free entries
- Linear traversal of valid entries

##### Tree layout

- Tree of entries
  - Filenames hashed into keys used to traverse tree
- E.g., XFS, NTFS

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.53.23 PM.png" alt="Screen Shot 2023-05-24 at 1.53.23 PM" style="zoom:50%;" />

###### Pros/Cons

- Fast search (~O(log N ) )
- Much more complicated to implement



### Index structures

#### Metadata to data

- From directory mapping, find metadata
- From metadata, find file's contents

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.54.39 PM.png" alt="Screen Shot 2023-05-24 at 1.54.39 PM" style="zoom:40%;" />

##### Goals

- Provide efficient sequential access to file's data
- Provide efficient random access to any file offset
- Limit overheads to be efficient for small files
- Be scalable to support large files

#### Contiguous allocation

- Files stored as a sequence of contiguous blocks
- File-to-blocks mapping includes first block (and size)
- Require allocation policy (e.g., first-fit, best-fit, worst-fit)

##### Example

<img src="Week 8.assets/Screen Shot 2023-05-24 at 1.56.00 PM.png" alt="Screen Shot 2023-05-24 at 1.56.00 PM" style="zoom:50%;" />

###### Pros

- Very simple
- Best performance
- Efficient sequential and random access

###### Cons

- Change in size likely to require entire reallocation
- External fragmentation

###### Usage

- ISO 9660 (CD-ROM, DVD, BD)

##### Digression about fragmentation

###### <u>Internal</u> fragmentation

- Waster space inside blocks
- Issue if blocks are too large
  <img src="Week 8.assets/Screen Shot 2023-05-25 at 10.46.56 PM.png" alt="Screen Shot 2023-05-25 at 10.46.56 PM" style="zoom:50%;" />

###### <u>External</u> fragmentation

- Free space scattered instead of being contiguous
- Issue if blocks need to be allocated contiguously
  <img src="Week 8.assets/Screen Shot 2023-05-25 at 10.47.08 PM.png" alt="Screen Shot 2023-05-25 at 10.47.08 PM" style="zoom:50%;" />

`Example: needs 4 free blocks for File 4: they exist but aren't contiguous`

#### Linked-list allocation

- Files stored as linked lists of blocks
- File-to-blocks mapping includes pointer to the first block
  - For each block, pointer to the next block in chain
  - Possibly, pointer to the last block to optimize file growth

##### Example

<img src="Week 8.assets/Screen Shot 2023-05-25 at 10.50.43 PM.png" alt="Screen Shot 2023-05-25 at 10.50.43 PM" style="zoom:50%;" />

###### Pros

- Fairly simple
- File size flexibility
- No external fragmentation
- Easy sequential access

###### Cons

- Potentially inefficient sequential access
- No (true) random access

###### Usage

- MS-FAT, ECS150FS

`might need to change the project FAT to linked list...`



# 5/24 L8.2 - FS implementation (2)

### Recap

##### Implementation overview

`Manage physical mass storage`

- From pair `<filename, offset>`, find physical storage block efficiently

<img src="Week 8.assets/Screen Shot 2023-05-25 at 10.57.29 PM.png" alt="Screen Shot 2023-05-25 at 10.57.29 PM" style="zoom:50%;" />

##### Directories

<img src="Week 8.assets/Screen Shot 2023-05-25 at 10.57.38 PM.png" alt="Screen Shot 2023-05-25 at 10.57.38 PM" style="zoom:70%;" />

##### Index structures

- Contiguous allocation

<img src="Week 8.assets/Screen Shot 2023-05-25 at 10.57.46 PM.png" alt="Screen Shot 2023-05-25 at 10.57.46 PM" style="zoom:70%;" />

- Linked-list allocation

<img src="Week 8.assets/Screen Shot 2023-05-25 at 10.57.54 PM.png" alt="Screen Shot 2023-05-25 at 10.57.54 PM" style="zoom:70%;" />

### Index structures

#### Direct allocation

- File-to-blocks mapping includes direct pointers to each data block

##### Example

<img src="Week 8.assets/Screen Shot 2023-05-25 at 11.07.32 PM.png" alt="Screen Shot 2023-05-25 at 11.07.32 PM" style="zoom:50%;" />

`Meta data would be huge`

##### Pros

- File size flexibility (no external fragmentation)
- Supports true random access

##### Cons

- Limited file size
- Non-scalable index structure

#### Indexed allocation

- File-to-blocks mapping includes a pointer to an index block
  - An index block contains an array of data block pointers
  - Data blocks allocated only on demand

##### Example

<img src="Week 8.assets/Screen Shot 2023-05-25 at 11.08.51 PM.png" alt="Screen Shot 2023-05-25 at 11.08.51 PM" style="zoom:50%;" />

##### Pros

- Same as direct allocation
- But decouple index structure from metadata

##### Cons

- Same as direct allocation
- But overhead for small files

#### Linked index blocks (IB + IB + ...)

- Last index block's pointer can point to next index block

##### Example

<img src="Week 8.assets/Screen Shot 2023-05-25 at 11.12.36 PM.png" alt="Screen Shot 2023-05-25 at 11.12.36 PM" style="zoom:50%;" />

##### Pros

- Can accommodate large files

##### Cons

- Traversal for very large files

#### Multilevel index blocks (IB x IB x ...)

- First-level index block points onto second-level index blocks
  - Tree structure

##### Example

<img src="Week 8.assets/Screen Shot 2023-05-25 at 11.14.36 PM.png" alt="Screen Shot 2023-05-25 at 11.14.36 PM" style="zoom:50%;" />

##### Pros

- Great support for very large files

##### Cons

- Wasteful for small files

### Case study: MS-FAT

#### Introduction

- Microsoft **File Allocation Table**
- Originally created for floppy disks, in the late 70s
  - Used on MS-DOS, and early version of Windows (before NTFS)
  - Still very popular on certain systems (e.g., thumb drives, camera SD cards, embedded systems, etc.)
- Different versions over time: FAT12, FAT16, FAT32, and now exFAT

##### File Allocation Table

- Index structure for files
  - Each file is a linked list of clusters
- Free space map
  - Linear map of all clusters on disk

<img src="Week 8.assets/Screen Shot 2023-05-25 at 11.21.41 PM.png" alt="MS-FAT image" style="zoom:50%;" />

#### FAT structure

- 1 entry per cluster (data block)
  <img src="Week 8.assets/Screen Shot 2023-05-25 at 11.24.02 PM.png" alt="Screen Shot 2023-05-25 at 11.24.02 PM" style="zoom:50%;" />
  

##### Index structures

- Directory entry maps name to first cluster index

| File    | Size  | Index |
| ------- | ----- | ----- |
| foo.txt | 18000 | 9     |
| bar.txt | 5000  | 12    |

- Indicates next cluster in chain 
  - or `EOC` for last cluster of a file

##### Free space tracking

- 0 indicates free cluster

##### Locality heuristics

- Usually simple allocation strategy (e.g. *next-fit*)
  - `When we want to look for the empty entry, we look from the last entry that we allocated`
  - `In project, we using first-fit, going back to the beginning of the FAT all the time.`

#### Directory structure

- Directory is a file containing an array of 32-byte entries.
- Each entry composed of
  - 8-byte name + 3-byte extension (ASCII)
    - *Long file names were later supported by allowing to chain multiple directory entries*
  - Creation date and time
  - Last modification date and time
  - Index of first cluster in FAT
  - Size of the file

<img src="Week 8.assets/Screen Shot 2023-05-25 at 11.31.46 PM.png" alt="Screen Shot 2023-05-25 at 11.31.46 PM" style="zoom:50%;" />

#### Layout on disk

<img src="Week 8.assets/Screen Shot 2023-05-25 at 11.36.50 PM.png" alt="Screen Shot 2023-05-25 at 11.36.50 PM" style="zoom:50%;" />

#### Locality heuristics

- Clusters for a file may be scattered across the disk
- *Defragmentation* can rearrange clusters and improve spatial locality

##### Before

<img src="Week 8.assets/Screen Shot 2023-05-25 at 11.37.55 PM.png" alt="Screen Shot 2023-05-25 at 11.37.55 PM" style="zoom:50%;" />

##### After

<img src="Week 8.assets/Screen Shot 2023-05-25 at 11.38.13 PM.png" alt="Screen Shot 2023-05-25 at 11.38.13 PM" style="zoom:50%;" />

#### Conclusion

##### Pros

- Simple
  - State required per file: start cluster and size
- Widely supported (maybe even the most popular FS ever!)
- No external fragmentation (all available clusters can be allocated)

##### Cons

- Limited performance
  - Many seeks if FAT cannot be cached in memory
  - Poor  locality for sequential access if files are fragmented
  - Poor random access
- Limited metadata
  - No access control
  - No support for hard links
- Limited volume and file sizes
  - E.g., 2-TiB max volume and 4-GiB max file size with FAT32
- No support for reliability strategies

