### Chapter 3 Sequential Logic

##### Sequential Logic

- With sequential logic the output is dependent not only on the current input but also on its current state
  - Its current state is based upon the previous inputs it received
  - This is in contrast to combinational logic where the output is dependent solely on the current input
- To be able to store state we need to be able to remember values but how to do that in circuitry? 

##### Memory - A first Attempt

- Case 1: Q = 0
  - I2 receives 0 and outputs 1
  - I1 receives 1 and outputs 0
- Case 2: Q = 1
  - I2 receives 1 and outputs 0
  - I1 receives 0 and outputs 1
- This circuit is bistable because it has two different stable states it can be in
- While this could be used to store a value the problem is we can’t change it

<img src="afterMTLecture.assets/G5P9oTaWdGdsxND6SvphVEb2M_JdSdvWXoEoRUuv5SghhGps47SgZiHZiqKbqCD5iM_bLOjZuAzQQdJgTclxdgW722DMuZF0d1yRhD81oT-qvzdvmIwfTBVDJPDf0I19_Fvb_lQZH7asTmJzaP0GOZ-t=s2048.png" alt="img" style="zoom:60%;" />

##### SR Latch (S for set, R for Reset)

- Case 1: R = 1, S = 0
  - 1 NOR Anything is 0 so N1 outputs 0
  - N2 receives two 0’s which is 1
  - Q = 0, ~Q = 1
- Case 2: R = 0, S = 1
  - 1 NOR Anything is 0 so N2 outputs 0
  - N2 receives two 0’s so outputs 1
  - Q = 1, ~Q = 0
- Case 3: S = 1, R = 1
  - 1 NOR Anything is a 0 so both gates output 1
  - Q = 0, ~Q = 0
- Danger both Q and ~Q have the same value so you should never set both S and R to 1

<img src="afterMTLecture.assets/bT9aQy1FHfMz0TO85RwSXQRCidociwtM7KVJE1Nb0nbf9eDr2QvHPkmBvNTI6LM0mF28L_hOReAeCvkQDaS_0bTS7AAcdbk8HVMVP4lR-mTwJcVp3BfRs9iGiyNuZ6XoQ_06kAdP0AeIaElEpALO8FST=s2048.png" alt="img" style="zoom:60%;" />





## [Latches and Flip-Flops (Lecture 02-06-2023)](https://video.ucdavis.edu/media/ECS154ALecture02-06-2023/1_4k63al1l)

- Case 4: R = 0, S = 0

  - 0 NOR Something depends on what the something is so we have to look at values of Q to get the behavior of the Circuit

- Case 4A: Q = 0

  - 0 and 0 both go into N2 so N2 outputs a 1 and ~Q is 1
  - 1 and 0 go into N1 so N1 outputs 0
  - Q = 0, ~Q = 1

- Case 4B: Q = 1

  - 1 and 0 both go into N2 and so N2 outputs 0

  - Two 0’s go into N1 so N1 outputs 1

  - Q = 1, ~Q = 0

  - <img src="afterMTLecture.assets/Screen Shot 2023-02-21 at 4.30.30 AM.png" alt="Screen Shot 2023-02-21 at 4.30.30 AM" style="zoom:50%;" />

    `R = 0, S = 0, the output store the behavior before, R = 1 means reset the Q to be 0. S = 1 means set the value to be 1`



##### SR - Latch (With Clock)

- The implementation on the previous slide allows us to choose what values to store but doesn’t provide any control for when we store the values
- To do this we add the Clock control signal

| CLK  | S    | R    | Q    |
| ---- | ---- | ---- | ---- |
| 0    | d    | d    | Q    |
| 1    | 0    | 0    | Q    |
| 1    | 0    | 1    | 0    |
| 1    | 1    | 0    | 1    |
| 1    | 1    | 1    | X    |

![img](afterMTLecture.assets/cNNO6k9akV6pskvhUeCRJwDjWQvP3cdCWPrfPBwZgagbaS-r_qyuFHxIfDmb0sBzaROxZFMar7H6-hcUvL9lsB6k28UBbcqmxo7PV9DJjCCvDpVWktbyn6G5Qz1rvjpkfIDJJy6AMD2ntZcfRdphZbdU=s2048.png)

`when the clock is 0, we "stay same" for the Q`

<img src="afterMTLecture.assets/Screen Shot 2023-02-21 at 4.38.50 AM.png" alt="Screen Shot 2023-02-21 at 4.38.50 AM" style="zoom:50%;" />



| D    | Q‘   |
| ---- | ---- |
| 0    | 0    |
| 1    | 1    |

| D    | Q    | Q' (new value of Q) |
| ---- | ---- | ------------------- |
| 0    | 0    | 0                   |
| 0    | 1    | 0                   |
| 1    | 0    | 1                   |
| 1    | 1    | 1                   |

Truth Table for SR Latch

| S    | R    | Q'   |
| ---- | ---- | ---- |
| 0    | 0    | Q    |
| 0    | 1    | 0    |
| 1    | 0    | 1    |
| 1    | 1    | X    |

Final Output

| D    | Q    | S    | R    |
| ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | d    |
| 0    | 1    | 0    | 1    |
| 1    | 0    | 1    | 0    |
| 1    | 1    | d    | 0    |

K-Map for S

| D/Q  | 0    | 1    |
| ---- | ---- | ---- |
| 0    | 0    | 1    |
| 1    | 0    | d    |

`S = D`

K-map for R

| D/Q  | 0    | 1    |
| ---- | ---- | ---- |
| 0    | d    | 0    |
| 1    | 1    | 0    |

`R = !D`



##### D - Latch

- In order to resolve the inconsistency for when both S and R are 1 we can instead use a D Latch 
- A D Latch just stores the value that it is given
- A D Latch can be built from an SR Latch and some other logic as follows 
- S = D 
- R = ~D

| D    | Q    |
| ---- | ---- |
| 0    | 0    |
| 1    | 1    |

<img src="afterMTLecture.assets/Screen Shot 2023-02-21 at 5.14.01 AM.png" alt="Screen Shot 2023-02-21 at 5.14.01 AM" style="zoom:50%;" />





##### T Latch

- The T in T latch stands for toggle



- Goal

| T    | Q    |
| ---- | ---- |
| 0    | Q    |
| 1    | ~Q   |



- | D    | Q‘   |
  | ---- | ---- |
  | 0    | 0    |
  | 1    | 1    |



- Figure out what to put in D

- | Q    | T    | D    |
  | ---- | ---- | ---- |
  | 0    | 0    | 0    |
  | 0    | 1    | 1    |
  | 1    | 0    | 1    |
  | 1    | 1    | 0    |

  The output is a XOR Gate

  <img src="afterMTLecture.assets/Screen Shot 2023-02-21 at 5.22.00 AM.png" alt="Screen Shot 2023-02-21 at 5.22.00 AM" style="zoom:50%;" />



##### JK Latch

- The JK Latch behaves like the SR latch with J being S and K being R but it has a defined behavior for when both J and S are 1, it toggles the value of Q

| J    | K    | Q    |
| ---- | ---- | ---- |
| 0    | 0    | Q    |
| 0    | 1    | 0    |
| 1    | 0    | 1    |
| 1    | 1    | ~Q   |



##### Master Slave D Flip Flop

- Flip Flops are similar to latches but instead of allowing values to change when the clock is 1 they can only change values when 
  - The clock transitions from 0 to 1 (rising edge triggered) 
  - Or the clock transitions from 1 to 0 (falling edge) 
- We can build a D Flip Flop from two D latches as is shown on the right

<img src="afterMTLecture.assets/lFtDNmtxb0OdmbE92_chnNglPdGv7d1KLpB-gJuIax0k4lE-UpGi-oMh3DtkT59DBTVBjdH_PbpU0wq0drq7moIZ-CQd9KWb70rGCSfIZ5YeslqvVb1bZRuzZDSv8Dy41zbFQyo0Jvqh55FFrK6GYl6H=s2048.png" alt="img" style="zoom:60%;" />

<img src="afterMTLecture.assets/Screen Shot 2023-02-21 at 5.29.46 AM.png" alt="Screen Shot 2023-02-21 at 5.29.46 AM" style="zoom:50%;" />

 



## [Finish Flip-Flops, Start Sequential Circuit Design(Discussion 02-06-2023)](https://video.ucdavis.edu/media/ECS154ADiscussion02-06-2023/1_2ioh2u92)

###### T - Flip Flop Truth Table

| D    | Q‘   |
| ---- | ---- |
| 0    | 0    |
| 1    | 1    |

| T    | Q‘   |
| ---- | ---- |
| 0    | Q    |
| 1    | ~Q   |

| Q    | T    | D                                                            |
| ---- | ---- | ------------------------------------------------------------ |
| 0    | 0    | (T is 0, we want Q' = Q based on table 2, so Q' is 0, so based on table 1.   D = 0) 0 |
| 0    | 1    | (T is 1, we want our value to be = ~Q, so Q' is 1, so D = 1) 1 |
| 1    | 0    | (T is 0, we want our value to be = Q, so Q' = 1, so D = 1) 1 |
| 1    | 1    | (T is 1, we want out value to be = ~Q, so Q' = 0, so D = 0) 0 |

###### SR Latch

| D    | Q‘   |
| ---- | ---- |
| 0    | 0    |
| 1    | 1    |

| S    | R    | Q'   |
| ---- | ---- | ---- |
| 0    | 0    | Q    |
| 0    | 1    | 0    |
| 1    | 0    | 1    |
| 1    | 1    | X    |

Output (Fill in S and R)

| D    | Q    | S                                                            | R       |
| ---- | ---- | ------------------------------------------------------------ | ------- |
| 0    | 0    | (D is 0, I want Q' = 0 based on Table 1, based on Table 2 row 1 and 2 Q' = 0, so S = 0 and R = 0/1) 0 | (0/1) d |
| 0    | 1    | (D is 0, I want Q' = 0, based on Table 2 row 2 Q' = 0, so S = 0 R = 1)   0 | 1       |
| 1    | 0    | (D is 1, so I want Q' = 1, based on Table 2 row 3 Q' = 1) 1  | 0       |
| 1    | 1    | (D is 1, so I want Q' = 1, based on Table 2 row 1 and 3 Q' = 1, so S = 1 or 0) d | 0       |



##### Synchronous Circuits

- Synchronous circuits are built using only 
  - Flip Flops 
  - Combinational Logic 
- All Flip Flops use the same clock 
  - This is like the beat of a drum on a rowboat in that everyone moves in time with the clock 
- While not as flexible as asynchronous circuits due to component constraints and the need to share a clock, they are easier to design and less prone to error 
- These benefits are so great that almost all circuits are synchronous circuits

 <img src="afterMTLecture.assets/7rbG_wjvJTRkm5-lMWoteZaJJQgPQ1eVZa0ml3nM6p_bdsxJeC7vNc3k-eWrgKi5d_4YrRjWGd-DdzPrKNS38dej2IkeK1bHSPJKImnJa6KjlCnHHPgoWCRDE7KcQajcT1d4cJvtRzF60tW2VEwxPzt7=s2048.png" alt="img" style="zoom:60%;" />



##### Finite State Machine Design Process

1. Choose whether you want to implement a Moore or Mealy Model Machine 
2. Draw the state diagram 
3. Simplify the state diagram 
4. Choose encodings for the states 
5. Choose Flip Flop Type 
6. Derive the next state equations 
7. Derive the output equations 
8. Build the circuit



##### Moore and Mealy Model Machines

- Moore the output is dependent only on the current state 
- Mealy the output is dependent on the current state and current input 
- Both use the current state and current inputs to find the next state to go to
- A is a Moore
- B is a Mealy

<img src="afterMTLecture.assets/7rbG_wjvJTRkm5-lMWoteZaJJQgPQ1eVZa0ml3nM6p_bdsxJeC7vNc3k-eWrgKi5d_4YrRjWGd-DdzPrKNS38dej2IkeK1bHSPJKImnJa6KjlCnHHPgoWCRDE7KcQajcT1d4cJvtRzF60tW2VEwxPzt7=s2048-20230222001958990.png" alt="img" style="zoom:60%;" />



##### Draw the State Diagram

- This is the part where you have to think
- The state diagram is problem dependent
- Moore Model Machine the outputs are inside the states
- Mealy Model Machine the outputs are on the edges



##### Simplify the state diagram

- Two FSMs are equivalent if they always have the same output for all inputs 
- Group together all states that will output the same value when given the same input into a super state 
  - Intuition: If the states output the same values on the same inputs they might be equivalent 
- Label each super state 
- For each super state 
  - For each state in the super state group them together if they transition to the same super states on the same inputs 
  - Close the groups created to prevent mistakes
- Repeat the last two steps until there is no change in the groupings



###### Example: output1 if there were at least 2 1's in the last group of 3 bits seen

![Screen Shot 2023-02-22 at 12.43.08 AM](afterMTLecture.assets/Screen Shot 2023-02-22 at 12.43.08 AM.png)

`The above diagram max the information, but it could be simpler`



## [State Simplification (Lecture 02-08-2023)](https://video.ucdavis.edu/media/ECS154ALecture02-08-2023/1_6sz7jzs3)

###### Review

- Difference between Moore and Mealy Model Machiens
  - Moore the output is dependent only on the current state
  - Mealy the output is dependent on the current state and current input
- Draw the State Diagram
  - We drew the previous diagram that remember all the information possible.



##### Simplify the state diagram

| A    | 0's  | si   | s0   | s1   | s00  | S01  | s10  | s11  | s000 | s001 | s010 | s100 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| B    | 1's  | s011 | s101 | s110 | s111 |      |      |      |      |      |      |      |

`Group states together by the output`

###### **A group**

| 0/1       |      |      |      |      |      |      |
| --------- | ---- | ---- | ---- | ---- | ---- | ---- |
| A/A (*A*) | Si   | S0   | S1   | S00  | S000 | S100 |
| A/B (*B*) | S10  | S01  | S001 | S010 |      |      |
| B/B (*C*) | S11  |      |      |      |      |      |

###### **B group**

| 0/1       |      |      |
| --------- | ---- | ---- |
| B/B (*D*) | S011 | S111 |
| A/B (*E*) | S101 | S110 |



`Then we need to rename the states`

New *A* group

| 0/1       |      |      |      |      |
| --------- | ---- | ---- | ---- | ---- |
| A/A (*a*) | Si   |      |      |      |
| A/B (*b*) | S0   | S00  | S000 | S100 |
| B/C (*c*) | S1   |      |      |      |

New *B* group

| 0/1       |      |      |
| --------- | ---- | ---- |
| A/E (*e*) | S10  | S010 |
| B/D (*f*) | S01  | S001 |

New *C* group

- Only S11, cannot breakup with others

New *D* group

| 0/1       |      |      |
| --------- | ---- | ---- |
| E/D (*g*) | S011 | S111 |

New *E* group

| 0/1       |      |
| --------- | ---- |
| B/D (*h*) | S101 |
| A/E (*i*) | S110 |



`We regroup again`

New *a* group

- Only Si

New *b* group

| 0/1  |      |      |      |      |
| ---- | ---- | ---- | ---- | ---- |
| B/E  | S0   | S00  | S000 | S001 |

New *c* group

- Only S1

New *d* group

| 0/1  |      |      |
| ---- | ---- | ---- |
| B/H  | S10  | S010 |

New *e* group

| 0/1  |      |      |
| ---- | ---- | ---- |
| D/G  | S01  | S001 |

New *f* group

- Only S11

New *g* group

| 0/1  |      |      |
| ---- | ---- | ---- |
| I/G  | S011 | S111 |

New *h* group

- Only S101

New *i* group

- Only S110

`Since they can't be change group further, so we could draw the new simplified diagram`



##### Simplified Diagram

<img src="afterMTLecture.assets/Screen Shot 2023-02-22 at 3.14.20 PM.png" alt="Screen Shot 2023-02-22 at 3.14.20 PM" style="zoom:70%;" />



## [State Encoding, Transition Tables, Next State Equations (Lecture 02-10-2023)](https://video.ucdavis.edu/media/ECS154ALecture02-10-2022/1_m015iskj)

##### Choose Encodings

- We need to pick a binary representation for our states to represent them in hardware 
- Encodings are arbitrary but choosing good encodings can simplify state transition logic 
- One way to choose a decent encoding is to place the states inside of a Kmap

- Place the states that transition to each other next to each other 
  - If they transition to each other and are next to each other in the Kmap we will be able to eliminate terms in their transition functions 
- The state’s encoding will be based on its location in the Kmap

`since we have 9 states, we have to use 4 bits (3 bits represents 8 states)`

###### Encoding

| S3S2/S1S0 | 00   | 01   | 11   | 10   |
| --------- | ---- | ---- | ---- | ---- |
| 00        | Si   | S1   | S11  | S110 |
| 01        | S*_0 | S_10 | S*11 | d    |
| 11        | S_01 | S101 | d    | d    |
| 10        | d    | d    | d    | d    |

###### Transition Table

- Bit Input = 0

| S3S2/S1S0 | 00   | 01   | 11   | 10   |
| --------- | ---- | ---- | ---- | ---- |
| 00        | S*_0 | S_10 | S110 | S*_0 |
| 01        | S*_0 | S*_0 | S110 | d    |
| 11        | S_10 | S_10 | d    | d    |
| 10        | d    | d    | d    | d    |

- Bit Input = 1

| S3S2/S1S0 | 00   | 01   | 11   | 10   |
| --------- | ---- | ---- | ---- | ---- |
| 00        | S1   | S11  | S*11 | S101 |
| 01        | S_01 | S101 | S*11 | d    |
| 11        | S*11 | S*11 | d    | d    |
| 10        | d    | d    | d    | d    |



##### Using Flip-Flop

###### Use D Flip Fop to store S3

| D    | Q_next |
| ---- | ------ |
| 0    | 0      |
| 1    | 1      |



- Bit input 0

| S3S2/S1S0 | <u>0</u>0    | <u>0</u>1    | <u>1</u>1    | <u>1</u>0    |
| --------- | ------------ | ------------ | ------------ | ------------ |
| 00        | S*_0 / **0** | S_10 / **0** | S110 / **1** | S*_0 / **0** |
| 01        | S*_0 / **0** | S*_0 / **0** | S110 / **1** | d            |
| 11        | S_10 / **0** | S_10 / **0** | d            | d            |
| 10        | d            | d            | d            | d            |

- Bit input 1

| S3S2/S1S0 | <u>0</u>0    | <u>0</u>1    | <u>1</u>1    | <u>1</u>0    |
| --------- | ------------ | ------------ | ------------ | ------------ |
| 00        | S1 / **0**   | S11 / **1**  | S*11 / **1** | S101 / **0** |
| 01        | S_01/ **0**  | S101 / **0** | S*11 / **1** | d            |
| 11        | S*11 / **1** | S*11 / **1** | d            | d            |
| 10        | d            | d            | d            | d            |

##### K-map

<img src="afterMTLecture.assets/Screen Shot 2023-02-22 at 6.53.32 PM.png" alt="Screen Shot 2023-02-22 at 6.53.32 PM" style="zoom:80%;" />

Red: S3S2

Brown: S1*Bit Input

Green: S2!S0 * Bit Input



###### Using T Flip Flop to store S2

 | T    | Q    |
  | ---- | ---- |
  | 0    | Q    |
  | 1    | ~Q   |

`Bits are same, put 0. Bits are different, put 1`

- Bit input 0

| S3S2/S1S0 | 0<u>0</u>                  | 0<u>1</u>                  | 1<u>1</u>                  | 1<u>0</u>    |
| --------- | -------------------------- | -------------------------- | -------------------------- | ------------ |
| 00        | S*_0 / **0**               | S_10 / **0**               | S110 / **1** (will change) | S*_0 / **0** |
| 01        | S*_0 / **0**               | S*_0 / **1** (will change) | S110 / **1**               | d            |
| 11        | S_10 / **1** (will change) | S_10 / **0**               | d                          | d            |
| 10        | d                          | d                          | d                          | d            |

- Bit input 1

| S3S2/S1S0 | 0<u>0</u>    | 0<u>1</u>    | 1<u>1</u>    | 1<u>0</u>    |
| --------- | ------------ | ------------ | ------------ | ------------ |
| 00        | S1 / **1**   | S11 / **0**  | S*11 / **0** | S101 / **1** |
| 01        | S_01/ **0**  | S101 / **0** | S*11 / **0** | d            |
| 11        | S*11 / **1** | S*11 / **0** | d            | d            |
| 10        | d            | d            | d            | d            |

Solve the K-Map

Red: S3*S2*~Bit Input

Blue: ~S2 * S1

Green: !S2!S0 * Bit Input



###### Using SR Flip Flop to store S1

| S    | R    | Q Next |
| ---- | ---- | ------ |
| 0    | 0    | Q      |
| 0    | 1    | 0      |
| 1    | 0    | 1      |
| 1    | 1    | X      |

| Q    | Q Next | S    | R    |
| ---- | ------ | ---- | ---- |
| 0    | 0      | 0    | d    |
| 0    | 1      | 1    | 0    |
| 1    | 0      | 0    | 1    |
| 1    | 1      | d    | 0    |

- S behave like D, you put in what you want, except in the case from 1 -> 1 it is don't care
- R behaves like ~D, you put in the **opposite** of what you want, except for the case from 0->0 is don't care



## [Finish Next State Equations, Build Circuit (Lecture 02-13-2023)](https://video.ucdavis.edu/media/ECS154ALecture02-13-2023/1_39zg2edc)

##### SR Flip Flop for S1

###### S K-map

- Original Encoding

| S3S2/S1S0 | 00   | 01   | 11   | 10   |
| --------- | ---- | ---- | ---- | ---- |
| 00        | Si   | S1   | S11  | S110 |
| 01        | S*_0 | S_10 | S*11 | d    |
| 11        | S_01 | S101 | d    | d    |
| 10        | d    | d    | d    | d    |

- Bit Input = 0

| S3S2/S1S0 | 00           | 01           | 11           | 10           |
| --------- | ------------ | ------------ | ------------ | ------------ |
| <u>0</u>0 | S*_0 / **0** | S_10 / **0** | S110 / **0** | S*_0 / **0** |
| <u>0</u>1 | S*_0 / **0** | S*_0 / **0** | S110 / **0** | d            |
| <u>1</u>1 | S_10 / **0** | S_10 / **0** | d            | d            |
| <u>1</u>0 | d            | d            | d            | d            |

`Q is current state, Q next is after transition state.` `If transition from 1 to 1, it's d`

- Bit Input = 1

| S3S2/S1S0 | 00           | 01           | 11          | 10           |
| --------- | ------------ | ------------ | ----------- | ------------ |
| <u>0</u>0 | S1 / **0**   | S11 / **0**  | S*11 /**0** | S101 / **1** |
| <u>0</u>1 | S_01/ **1**  | S101 / **1** | S*11 /      | d            |
| <u>1</u>1 | S*11 / **0** | S*11 / **0** | d           | d            |
| <u>1</u>0 | d            | d            | d           | d            |

###### R K-map

- Bit Input = 0

| S3S2/S1S0 | 00           | 01           | 11           | 10           |
| --------- | ------------ | ------------ | ------------ | ------------ |
| <u>0</u>0 | S*_0 / **d** | S_10 / **d** | S110 / **d** | S*_0 / **d** |
| <u>0</u>1 | S*_0 / **d** | S*_0 / **d** | S110 / **d** | d            |
| <u>1</u>1 | S_10 / **1** | S_10 / **1** | d            | d            |
| <u>1</u>0 | d            | d            | d            | d            |

`example: S*_0 S3 transition from 0 to 0, so it is d`

- Bit Input = 1

| S3S2/S1S0 | 00           | 01           | 11           | 10           |
| --------- | ------------ | ------------ | ------------ | ------------ |
| <u>0</u>0 | S1 / **d**   | S11 / **d**  | S*11 /**d**  | S101 / **0** |
| <u>0</u>1 | S_01 / **0** | S101 / **0** | S*11 / **d** | d            |
| <u>1</u>1 | S*11 / **1** | S*11 / **1** | d            | d            |
| <u>1</u>0 | d            | d            | d            | d            |

###### K-map

<img src="afterMTLecture.assets/Screen Shot 2023-02-22 at 8.30.44 PM.png" alt="Screen Shot 2023-02-22 at 8.30.44 PM" style="zoom:50%;" />

S1 S input

Red: `Bitinput * !S3!S1S0`

Green: `Bitinput * S3!S2`

<img src="afterMTLecture.assets/Screen Shot 2023-02-22 at 8.33.07 PM.png" alt="Screen Shot 2023-02-22 at 8.33.07 PM" style="zoom:50%;" />

S1 R input

Red: `S1`



##### JK Flip Flop

- Behavior

| J    | K    | Q Next |
| ---- | ---- | ------ |
| 0    | 0    | Q      |
| 0    | 1    | 0      |
| 1    | 0    | 1      |
| 1    | 1    | ~Q     |

| Q    | Q Next | J    | K    |
| ---- | ------ | ---- | ---- |
| 0    | 0      | 0    | d    |
| 0    | 1      | 1    | d    |
| 1    | 0      | d    | 1    |
| 1    | 1      | d    | 0    |

J K-map

- Bit Input = 0

| S3S2/S1S0 | 00           | 01           | 11           | 10           |
| --------- | ------------ | ------------ | ------------ | ------------ |
| 0<u>0</u> | S*_0 / **1** | S_10 / **1** | S110 / **0** | S*_0 / **1** |
| 0<u>1</u> | S*_0 / **d** | S*_0 / **d** | S110 / **d** | d            |
| 1<u>1</u> | S_10 / **d** | S_10 / **d** | d            | d            |
| 1<u>0</u> | d            | d            | d            | d            |

- Bit Input = 1

  | S3S2/S1S0 | 00           | 01           | 11           | 10           |
  | --------- | ------------ | ------------ | ------------ | ------------ |
  | 0<u>0</u> | S1 / **0**   | S11 / **0**  | S*11 / **1** | S101 / **1** |
  | 0<u>1</u> | S_01/ **d**  | S101 / **d** | S*11 / **d** | d / **d**    |
  | 1<u>1</u> | S*11 / **d** | S*11 / **d** | d            | d            |
  | 1<u>0</u> | d            | d            | d            | d            |

`When original state is 1, in J k-map it is don't care (d)`

K K-map

- Bit Input = 0

| S3S2/S1S0 | 00           | 01           | 11           | 10           |
| --------- | ------------ | ------------ | ------------ | ------------ |
| 0<u>0</u> | S*_0 / **d** | S_10 / **d** | S110 / **d** | S*_0 / **d** |
| 0<u>1</u> | S*_0 / **0** | S*_0 / **0** | S110 / **1** | d            |
| 1<u>1</u> | S_10 / **0** | S_10 / **0** | d            | d            |
| 1<u>0</u> | d            | d            | d            | d            |

- Bit Input = 1

| S3S2/S1S0 | 00           | 01           | 11           | 10           |
| --------- | ------------ | ------------ | ------------ | ------------ |
| 00        | S1 / **d**   | S11 / **d**  | S*11 / **d** | S101 / **d** |
| 01        | S_01 / **0** | S101 / **0** | S*11 / **0** | d            |
| 11        | S*11 / **0** | S*11 / **0** | d            | d            |
| 10        | d            | d            | d            | d            |

`when original state is 0, in k kmap, it is don't care`

###### K-Map

Red: S3 + !Bit Input

Green: !S3 + !S2 + Bit Input



##### Output

| S3S2/S1S0 | 00           | 01           | 11           | 10          |
| --------- | ------------ | ------------ | ------------ | ----------- |
| 00        | Si / **0**   | S1 / **0**   | S11/ **0**   | S110 /**1** |
| 01        | S*_0 / **0** | S_10 / **0** | S*11 / **1** | d           |
| 11        | S_01 / **0** | S101 / **1** | d            | d           |
| 10        | d            | d            | d            | d           |



##### Build the Circuit

`Logism can build the circuits for you! Using Project -> Analyze Circuit`

 [FlipFlopLecture.circ](../../../../../../../Logism/FlipFlopLecture.circ) 



## [Run Moore Circuit, Sequential Circuit Testing, Start Mealy Model Example (Discussion 02-13-2023)](https://video.ucdavis.edu/media/ECS154ADiscussion02-13-2023/1_v0vt9ugu)

##### Build the Circuit

 [FlipFlopLecture.circ](../../../../../../../Logism/FlipFlopLecture.circ) 

`Use Simulate - Tick to check the output`

`Sequential Circuit Testing`

##### Mealy Model Example

In the last group of 4 bits did the pattern **1010** or **1100** appear

```mermaid
stateDiagram-v2
    Si --> S0: 0 / 0
		Si --> S1: 1 / 0
		S0 --> S00: 0 / 0
		S0 --> S01: 1 / 0
		S00 --> S000:0 / 0
		S00 --> S001:1 / 0
		S01 --> S010:0 / 0
		S01 --> S011: 1 / 0
		S1 --> S10:0 / 0
		S1 --> S11:1 / 0
		S10 --> S100:0 / 0
		S10 --> S101:1 / 0
		S11 --> S110:0 / 0
		S11 --> S111:1 / 0
		S000 --> newSi: d / 0
    S001 --> newSi: d / 0
    S010 --> newSi: d / 0
    S011 --> newSi: d / 0
    S100 --> newSi: d / 0
    S101 --> newSi:0 / 1, 1 / 0
    S110 --> newSi:0 / 1, 1 / 0
```

`0 / 0:  get a 0 and output a 0`

`How is it different between Mealy and Moore?`

- The output of mealy depends on input and state, we don't need to write another state

| 0/0, 1/0 (Group A) | Si     | S0   | S00  | S01  | S10  | S11  | S000 | S100 | S111 | S1   | S001 | S011 | S010 |
| ------------------ | ------ | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0/1, 1/0 (Group B) | S101   | S110 |      |      |      |      |      |      |      |      |      |      |      |
| 0/0, 1/1           | (None) |      |      |      |      |      |      |      |      |      |      |      |      |
| 0/1, 1/1           | (None) |      |      |      |      |      |      |      |      |      |      |      |      |

Mealy: Number Starting States can be up to 2 ^ (number of inputs + number of outputs)

Moore: States can be up to 2 ^ (number of outputs)

| 0/1             |      |      |      |      |      |      |      |      |      |      |      |
| --------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| (Group *A*) A/A | Si   | S0   | S01  | S000 | S100 | S111 | S001 | S010 | S011 | S00  | S1   |
| (Group *B*) A/B | S10  |      |      |      |      |      |      |      |      |      |      |
| (Group *C*)B/A  | S11  |      |      |      |      |      |      |      |      |      |      |

Group *D*: S101, S110



------



| 0/1            |      |      |      |      |      |      |      |      |      |
| -------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| (Group *a*)A/A | Si   | S0   | S00  | S01  | S000 | S100 | S111 | S001 | S010 |
| (Group *b*)B/C | S1   |      |      |      |      |      |      |      |      |
| (Group *c*)    | S10  |      |      |      |      |      |      |      |      |
| (Group *d*)    | S11  |      |      |      |      |      |      |      |      |
| (Group *e*)    | S101 | S110 |      |      |      |      |      |      |      |

------

| 0/1               |      |      |      |      |      |      |      |      |      |
| ----------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| (Group **a**) A/B | Si   |      |      |      |      |      |      |      |      |
| (Group **b**) A/A | S0   | S00  | S01  | S000 | S100 | S111 | S001 | S010 | S011 |
| (Group **c**)     | S10  |      |      |      |      |      |      |      |      |
| (Group **d**)     | S11  |      |      |      |      |      |      |      |      |
| (Group **e**)     | S101 | S110 |      |      |      |      |      |      |      |

------

| 0/1        |      |      |      |      |      |      |
| ---------- | ---- | ---- | ---- | ---- | ---- | ---- |
| **a**      | Si   |      |      |      |      |      |
| **b ** B/B | S0   | S00  | S01  |      |      |      |
| **c** A/A  | S000 | S100 | S111 | S001 | S010 | S011 |
| **d**      | S10  |      |      |      |      |      |
| **e**      | S11  |      |      |      |      |      |
| **f**      | S101 | S110 |      |      |      |      |

------

| 0/1   |      |      |      |      |      |      |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- |
| A     | Si   |      |      |      |      |      |
| B B/B | S0   |      |      |      |      |      |
| C C/C | S00  | S01  |      |      |      |      |
| D A/A | S000 | S100 | S111 | S001 | S010 | S011 |
| E     | S10  |      |      |      |      |      |
| F     | S11  |      |      |      |      |      |
| G     | S101 | S110 |      |      |      |      |



## [Finish Mealy Model Example, Register, Enabled D-FlipFlop (Lecture 02-15-2023)](https://video.ucdavis.edu/media/ECS154ALecture02-15-2023/1_lqgc32vf)

`To test your circuits, if the tester can't find your circuit, need to go to Library-> Logism Evolution Library-> Find your circuits. If change, reload the library. Right click to reload the library.`

`Combinational circuits should not be back to itself.`

| 0/1               |      |      |      |      |      |      |
| ----------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Si                | Si   |      |      |      |      |      |
| S0 B/B            | S0   |      |      |      |      |      |
| S1                | S1   |      |      |      |      |      |
| SGoingToBeBad C/C | S00  | S01  |      |      |      |      |
| SBad A/A          | S000 | S100 | S111 | S001 | S010 | S011 |
| S10               | S10  |      |      |      |      |      |
| S11               | S11  |      |      |      |      |      |
| SMightBeGood      | S101 | S110 |      |      |      |      |

**K-map**

| S2S1/S0 | 00   | 01   | 11            | 10              |
| ------- | ---- | ---- | ------------- | --------------- |
| 0       | Si   | S0   | S0d           | ThrreeBitsMatch |
| 1       | S10  | S1   | ThreeBitseBad | S11             |

**Simplified Diagram**

```mermaid
stateDiagram-v2
	Si --> S0: 0/0
	S0 --> S0d: d/0
	Si --> S1: 1/0
	S1 --> S10: 0/0
	S1 --> S11: 1/0
	S0d --> ThreeBitsBad: d/0
	S10 --> ThreeBitsMatch: 1/0
	S10 --> ThreeBitsBad: 0/0
	ThreeBitsBad --> Si: d/0
	S11 --> ThreeBitsMatch: 0/0
	S11 --> ThreeBitsBad: 1/0
	ThreeBitsMatch --> ToSi: 0/1
	ThreeBitsMatch --> Si: 1/0
```

`S0d = ThreeBitsBad	MightBe `

![image-20230227043525044](afterMTLecture.assets/image-20230227043525044.png)

##### Logism

 [MealyModel215Lecture.circ](..\..\..\Winter 2023\ECS 154A\MealyModel215Lecture.circ) 



## [Sequential Building Blocks, Sequential Circuit Timing, Find Min Clock Length (Lecture 02/17/2023)](https://video.ucdavis.edu/media/ECS154ALecture02-17-2023/1_1vns45f8)

##### Logism Examples

- Register
  - <img src="afterMTLecture.assets/image-20230227044450224.png" alt="image-20230227044450224" style="zoom:60%;" />
- Enabled Flip-Flop
  - <img src="afterMTLecture.assets/image-20230227044511189.png" alt="image-20230227044511189" style="zoom:60%;" />
- Shift Register
  - <img src="afterMTLecture.assets/image-20230227044623732.png" alt="image-20230227044623732" style="zoom:67%;" />


