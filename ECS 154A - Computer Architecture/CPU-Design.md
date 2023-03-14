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

  

 
