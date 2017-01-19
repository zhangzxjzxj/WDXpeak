# FCA Homework Problem Set

*Da Wang 2014.10.13*

##Chapter 1: Processor Design

**P1.3 Explain the differences between architecture, implementation, and realization. Explain how each of these relates to processor performance as expressed in Equation 1-1.**

Architecture specifies the interface between the hardware and the programmer and affects the number of instructions that need to be executed for a particular program or algorithm. Imple- mentation specifies the microarchitectural organization of the design, and determines the instruc- tion execution rate (measured in instructions per cycle) for that particular design. The realization actually maps the implementation to silicon, and determines the cycle time. The product of the three terms determines performance.

---

**P1.6 A program’s run time is determined by the product of instructions per program, cycles per instruction, and clock frequency. Assume the following instruction mix for a MIPS-like RISC instruction set: 15% stores, 25% loads, 15% branches, and 30% integer arithmetic, 5% integer shift, and 5% integer multiply. Given that stores require one cycle, load instructions require two cycles, branches require four cycles, integer ALU instructions require one cycle, and integer multiplies require ten cycles, compute the overall CPI.**

Type | Mix | Cost | CPI
:--: | :--:| :--: | :--:
store | 15% | 1 | 0.15
load | 25% | 2 | 0.5
branch | 15% | 4 | 0.6
integer | 30% | 1 | 0.3
shift | 5% | 1 | 0.05
multiply | 5% | 10 | 0.5
**Total** | 100% |  | **2.10**

---

**P1.7 Given the parameters of Problem 6, consider a strength-reducing optimization that converts multiplies by a compile-time constant into a sequence of shifts and adds. For this instruction mix, 50% of the multiplies can be converted to shift-add sequences with an average length of three instructions. Assuming a fixed frequency, compute the change in instructions per program, cycles per instruction, and overall program speedup.**

Type | Old Mix | New Mix | Cost | CPI
:--: | :--:| :--: | :--: | :--:
store | 15% | 15% | 1 | 0.15
load | 25% | 25% | 2 | 0.5
branch | 15% | 15% | 4 | 0.6
integer&shift | 35% | 42.5% | 1 | 0.425
multiply | 5% | 2.5% | 10 | 0.25
**Total** | 100% | 105% | | **1.925 / 105% = 1.83**

There are 5% more instructions per program, the CPI is reduced by 12.7% to 1.83, and overall speedup is 2.1/1.925 = 1.091 or 9.1%.

---

**P1.8 Recent processors like the Pentium 4 processors do not implement single-cycle shifts. Given the scenario of Problem 7, assume that s = 50% of the additional integer and shift instructions introduced by strength reduction are shifts, and shifts now take four cycles to execute. Recompute the cycles per instruction and overall program speedup. Is strength reduction still a good optimization?**

Type | Old Mix | New Mix | Cost | CPI
:--: | :--:| :--: | :--: | :--:
store | 15% | 15% | 1 | 0.15
load | 25% | 25% | 2 | 0.5
branch | 15% | 15% | 4 | 0.6
integer | 30% | 33.75% | 1 | 0.3375
shift | 5% | 8.75% | 4 | 0.35
multiply | 5% | 2.5% | 10 | 0.25
**Total** | 100% | 105% | | **2.1875 / 105% = 2.083**

Speedup is now a slowdown: 2.1/2.1875 = 0.96 or 4% slowdown, hence strength reduction is a bad idea.

---

**P1.9 Given the assumptions of Problem 8, solve for the break-even ratio s (percentage of additional instructions that are shifts). That is, find the value of s (if any) for which program performance is identical to the baseline case without strength reduction (Problem 6).**

	2.1 = 0.15+0.50+0.60+0.30+0.05x4+0.25 + (1-s)x0.075x1 + sx0.075x4 
	0.025 = 0.225s => s = 0.111 = 11.1%
	
**P1.11 Using Amdahl’s law,compute speedups for a program that is 85% vectorizable for a system with 4, 8, 16, and 32 processors. What would be a reasonable number of processors to build into a sys- tem for running such an application?**

	Sn,p = 1/[p/n + (1-p)]: p = 0.85; n=4, 8, 16:












## Chapter 2: Pipelined Processors

**P2.1 Equation 4(P43) -> P=1/(T/k+S), which relates the performance of an ideal pipeline to pipeline depth, looks very simi- lar to Amdahl’s law. Describe the relationship between the terms in these two equations, and develop an intuitive explanation for why the two equations are so similar.**

---




:--: | :--:| :--: | :--: | :--: | :--:| :--: | :--: | :--:
Load | 25% | 2 | 0.5 | 500 | 30.5% | 305 | 610 | 0.58
Store | 15% | 1 | 0.15 | 150 | 15% | 150 | 150 | 0.14
Arithmetric | 30% | 300 | 0.3 | 300 | 30% | 300 | 300 | 0.28
Logical | 10% | 1 | 0.1 | 100 | 10% | 100 | 100 | 0.09 
Branch-T | 8% | 3 | 0.24 | 240 | 4% | 40 | 120 | 0.11
Branch-NT | 6% | 2 | 0.12 | 120 | 4.5% | 45 | 90 | 0.09
Jump | 5% | 2 | 0.1 | 100 | 5% | 50 | 100 | 0.09
Jump register | 1% | 3 | 0.03 | 30 | 6.5% | 65 | 195 | 0.18
Total | 100% | | 1.54 | 1540 | 105.5% | 1055 | 1665 | 1.58

	Increase in CPi: 1.58/1.54 = 1.024805



	for(int i=0;i<1000;i += 2) {
	}


--- 

The first iteration accesses memory location &B[0], &C[1], and &A[0]. Unfortunately, since the arrays are consecutive in memory, these locations are exactly 8KB (1024 x 8B per double) apart. Hence, in a direct-mapped cache they conflict, and the access to C[1] will evict B[0], while the access to A[0] will evict C[1]. As a result, every reference will miss, leading to a 100% miss rate.

---

**P3.3 Given the example code in Problem 1, and assuming a virtually-addressed two-way set associa- tive cache of capacity 8KB and 64 byte blocks, compute the overall miss rate (number of misses divided by number of references). Assume that all variables except array locations reside in reg- isters, and that arrays A, B, and C are placed consecutively in memory.**

The first iteration accesses memory location &B[0], &C[1], and &A[0]. Unfortunately, since the arrays are consecutive in memory, these locations are exactly 8KB (1024 x 8B per double) apart. Hence, in a two-way set-associative cache they conflict, and the access to A[0] will evict B[0]. In the second iteration, the access to B[1] will evict C[1], and so on. However, since the access to C is offset by 1 double (8 bytes), in the seventh iteration it will access C[8], which does not conflict with B[7]. Hence, B[7] will hit, as will A[7]. In the eighth iteration, C[9] will also hit, but now B[8] and A[8] will again conflict, and no hits will result. Hence, there are three hits every eight iterations, leading to a total number of hits of floor(1000/8)*3 = 375 hits. The num- ber of misses is 3000-375 = 2625, for an overall miss rate of 87.5% misses per reference.

---

**P3.16 Assume a two-level cache hierarchy with a private level one instruction cache(L1I),aprivate level one data cache (L1D), and a shared level two data cache (L2). Given local miss rates for the 4% for L1I, 7.5% for L1D, and 35% for L2, compute the global miss rate for the L2 cache.**

L2 global miss rate = (0.35 L2 misses)/(L2 ref) x ((0.04 L2 ref)/Ifetch + (0.075 L2 ref) / D-ref) = 0.35x0.04 + 0.35x0.075 = 0.04025 = 4.025% L2 misses per global reference

---

**P3.17 Assuming 1 L1I access per instruction and 0.4 data accesses per instruction, compute the misses per instruction for the L1I, L1D, and L2 caches of Problem 16.**

L1 I misses per instruction = .04 miss/ref x 1 ref/instr = .04 miss/inst


---





---





---





---

**Example 1: Suppose we have a 32KB 2-way SA cache with 2 word blocks. Determine the size of the tag, index and offset fields if we're using a 32-bit architecture.**

- number of bits for offset: specify the correct byte within a block, depends on the size of block.
- number of bits for set index: specify the correct cache set, dpends on the number of sets
- number of bits for tag: remaining bits

Here is the calculation:
	
	cache contains 32 KB = 2^15 bytes
	each block contains 2^3 bytes(2 words), so 3 offset bits.
	a set has two blocks = 2^4 bytes
	the number of sets = 2^15 / 2^4 = 2^11, so 11 index bits.
	tag bits(the remaining) = 32 - 11 - 3 = 18 bits(left most)
	Total size = number of rows * size of each row 
		= number of rows * N * size of each block(the N-way SA)
	Total size = 2^11 rows * 2 * (64 bits + 18 bits + 1bits) 
		= 339968 bits = 42496 B > 32 KB
	(64 bits = 2 words, 18 bits -> tag, 1 bit -> valid bit) 

---

**Example 2: The system has the following properties:**

- The processor uses 32-bit physical addresses.
- Load/store instructions are performed in program order and each ties up all caches until it is complete.
- There is 2KB of L1 cache and 16KB of L2 cache.
- Both caches are physically indexed and tagged with a block size of 128 bytes
- Both caches are 4-way set associative and use the LRU replacement policy.
- The write policy in both caches is write-allocate, write-back.
- Dirty bits are used to allow silent replacement of clean blocks, so only dirty blocks are written back on replacement.
- A dirty block replaced from the L1 cache is sent to the L2 cache. If that block is still cached in the L2 cache, the L2 updates its version of the block, but does not update the block’s LRU counter in L2, and does not send the block to memory. If the block is not in L2, it is sent to main memory without putting it in L2.
- When there is an access that misses in both caches, the L2 cache is completely updated (including replacement, if any is needed) before forwarding the block to L1.
- Hits in the L1 cache do not go to the L2 cache and do not update the LRU in the L2 cache.
- Neither inclusion nor exclusion property is maintained.

**(a)For each cache, show how physical address FFF82754 is broken down into the offset, index and tag, showing the number of bits for each. Which set will this load go to in the L1 cache, and which in the L2 cache? How many tags will be checked to determine whether this address is a hit or a miss in the L1 cache?**

In both caches, the block has 128 bytes, so the lowermost 7 bits of the address are the offset.






**(b) Cache size tells us the number of data bytes that the cache can hold. However,the cache also has some overhead state (tags, valid, LRU, and dirty bits). Compute the number of number of overhead bits in the L1 cache in this problem. What is the ratio of the number of overhead bits and the number of data bits in the cache? Keeping all other specified parameters the same (cache size, associativity, etc.), what would this ratio be if the cache block size was changed to 32?**

For each line of data, we keep a valid bit, a 23-bit tag (see part a), a dirty bit, and 2 bits of LRU. In total, we have 27 (1+23+1+2) overhead bits for 1024 its (128 bytes * 8) of data, so the ratio is 27/1024=0.026 (2.6%).


---






1 bit. Page size is 2 KB. Hence, the page offset is 11 bits (bits 10:0).



---




**(b) What percentage of the cache memory is used for tag bits?**

For each cache line (block), we have 1 tag entry. The size of the cache line is 32 * 8 = 256 bits. Therefore, the percentage of cache memory used for tag bits is 48 / (48 + 256) = 15.8%.

	The number of blocks in the L2 cache = 1024KB / 64B = 16K 
	The number of sets in the L2 cache = 16K / 8 = 2K
	The number of blocks in the L2 cache = 8MB / 128B = 64K 
	The number of sets in the L2 cache = 64K / 2 = 32K


**Example 7: Consider a system with the following processor components and policies:**

- A direct-mapped L1 data cache of size 4KB and block size of 16 bytes, indexed and tagged using physical addresses, and using a write-allocate, write-back policy
- A fully-associative data TLB with 4 entries and an LRU replacement policy
- Physical addresses of 32 bits, and virtual addresses of 40 bits
- Byte addressable memory
- Page size of 1MB

**Which bits of the virtual address are used to obtain a virtual to physical translation from the TLB? Explain exactly how these bits are used to make the translation, assuming there is a TLB hit.**

The virtual address is 40 bits long. Because the virtual page size is 1MB = 2^20 bytes, and memory is byte addressable, the virtual page offset is 20 bits. Thus, the first 40- 20=20 bits are used for address translation at the TLB. Since the TLB is fully associative, all of these bits are used for the tag; i.e., there are no index bits.











	CPI = 1.5 + (0.25* 0.02 * 158) = 2.29

**B: Suppose we now replace the processor with an out-of-order processor and the cache with a non-blocking cache that can have multiple load and store misses outstanding. Such a configuration can overlap some part of the miss penalty, resulting in a lower effective penalty as seen by the processor. Assume that this configuration effectively reduces the miss penalty (as seen by the processor) by 20%. What is the CPI of this new system and what is the speedup over the system in Part A?**

	Effective miss penalty = 0.80 * 158 = 126 cycles.

**C: Start with the system in Part A for this part. Suppose now we double the bus width and the width of each memory bank. That is, it now takes 50 cycles for memory to access the block as before, and the additional 20 cycles now send a double word of data back from memory to the cache. What is the miss penalty now? What is the CPI? Is this system faster or slower than that in Part B?**

	Miss penalty = 8 + 50 + 25*2 = 108 cycles. 
	CPI = 1.5 + (0.25 * .02 * 108) = 2.04.


The tag bits(and the value bit) do not hold any cache data so they are not counted in the size of the cache.




---

**Example 1: In one or two sentences, explain the tradeoffs between adding an additional pipeline stage vs. adding a write port to the register file. What conditions might favor one or the other design?**

Increasing the ports in the register file increases its size quadratically. If the register file is the critical path in the pipeline, this will slow down the processor, and no matter what it increases area and power overheads. On the other hand, if applications commonly stall on the structural hazard due to many LDWAs, it may be worth it to add a write port to the register file. An additional stage can also complicate bypassing and stalling logic, although this is likely to be less expensive than expanding the register file. (The latency of the additional pipeline stage, ignoring stalls, is not a major concern.)













---

**Example 1: Suppose we have a deeply pipelined processor for which we implement a branch-target buffer (BTB) for conditional branches. Assume that the misprediction penalty is always 4 cycles and the buffer miss penalty is always 3 cycles. Assume 85% prediction accuracy and 20% frequency of conditional branches. What should the hit rate in the branch target buffer be for this processor to be faster than a processor that has a fixed 2-cycle branch penalty? (assume that the two processors are equivalent in every other respect.) Assume a base CPI of 1 when there are no conditional branch stalls.**

CPI of processor with a fixed 2-cycle branch penalty:



	= H * 0.15 * 4 + (1 – H) * 3 = 3 – 2.4H
	

	= 1 + 0.2 * (3 – 2.4H) 

The Overall CPI above must be less than 1.4


---

	int sum1 = 0, sum2 = 0, sum3 = 0, sum4 = 0;
	for (i = 0; i < 1000; i++) // Branch 1: Loop Branch
	{
		if (i % 4 == 0) // Branch 2: If Condition 1
			sum1 += array[i]; // Taken Path
		else
			sum2 += array[i]; // Not-Taken Path
		
		if (i % 2 == 0) // Branch 3: If Condition 2
			sum3 += array[i]; // Taken Path
		else
			sum4 += array[i]; // Not-Taken Path
	}

**(a) What is the prediction accuracy for each of the three branches using a per-branch last-time predictor(assume that every per-branch counter starts at "not-taken")? Please show all of your work.**

BRANCH 1: 998=1000 = 99:8%: The branch is mispredicted the rst time it's executed.

BRANCH 2: 500=1000  100 = 50%:

BRANCH 3: 0%. The branch changes direction every time it is executed.

**(b) What is the prediction accuracy for each of the three branches when a per-branch 2-bit saturating counter-based predictor is used (assume that every per-branch counter starts at "strongly not-taken")? Please show all of your work.**

BRANCH 1: 997 / 1000 * 100 = 99:7%: The branch is mispredicted the first two times it's executed and the last time(when the loop exits)

BRANCH 2: 750 / 1000 * 100 = 75%. The branch repeats the pattern TNNNTNNN... The saturating counter moves between "strongly not-taken" and "weakly not-taken" (once out of every four predictions, after the branch is actually taken), and the prediction is always not-taken.

BRANCH 3: 500 / 1000 * 100 = 75%. The branch repeats the pattern TNTN... The saturating counter moves between "strongly not-taken" and "weakly not-taken" every prediction, and every prediction is not-taken, which is correct half the time.
**(c) What is the prediction accuracy for both Branch 2 and Branch 3, when the counter starts at (i) "weakly not-taken" and (ii) "weakly taken"?**

(i) "weakly not-taken"

BRANCH 2: 749 / 1000 * 100 = 74.9%

BRANCH 3: 0%. The predictor oscillates between "weakly not-taken" and "weakly taken".

(ii) "weakly taken"

BRANCH 2: 749 / 1000 * 100 = 74.9%. The branch's pattern is TNNNTNNN... The first four iteration, the predictor's counter starts at weakly taken, moves to strongly taken, then weakly taken, then weakly not-taken. It thus predicts TTTN. Starting for the next group of four branches, the counter is strongly not-taken, weakly not-taken, strongly not-taken, strongly not-taken, yielding NNNN predictions. Thus 249*3+2 = 749 correct predictions are made.

BRANCH 3: 500 / 1000 * 100 = 50%. The branch pattern is TNTN... The counter is at weakly-taken for the first branch, moves to strongly-taken, then weakly taken, etc. Thus the predictions are TTTT.. so the predictor is 50% accurate.

**(d) What is the prediction accuracy for each of the three branches when a two-level global branch predictor with a two-bit global history register and a separate pattern history table per branch, consisting of 2-bit saturating counters for every entry, is used? Assume that both bits of the global history register are initialized to "not-taken" and the 2-bit saturating counters in the pattern history tables are initialized to "strongly not-taken". When calculating prediction accuracy, ignore the rst 500 loop iterations.**

BRANCH 1: 499/500 × 100 = 99.8% The predictor has already entered a “strongly taken” state for all global histories for this branch after 500 loop iterations. It always predicts taken. Only the last loop iteration (for which the loop branch is not taken) results in a misprediction.







:--: | :--:| :--: | :--: | :--:
ST | T | T | 1 | ST
ST | T | T | 1 | ST
ST | T | T | 1 | ST
ST | T | N | 0 | WT
WT | T | N | 0 | WN
WN | N | T | 0 | WT
WT | T | T | 1 | ST
ST | T | T | 1 | ST
ST | T | N | 0 | WT
WT | T | N | 0 | WN
WN | N | T | 0 | WT
WT | T | T | 1 | ST
ST | T | T | 1 | ST
ST | T | N | 0 | WT
WT | T | N | 0 | WN

This table shows the progression of the counter state, the counter’s predictions, the actual outcomes, whether the prediction was correct, and the transition to the next state. The column labeled “Actual Outcome” lists the original branch outcome sequence. For this sequence, there were 7 correct predictions out of 15 predictions overall for a 46.67% prediction rate (53.33% misprediction rate).

---

**P9.3 What is the minimum local history lenght needed to achieve perfect branch prediction for the branch outcome sequence used in Problem 9.2? Draw the corresponding PHT and fill in each entry wiht one or T(predict taken), N(predict not-taken), or X(doesn't matter).**

Even though the branch outcome pattern from Problem 2 repeats every five branches, a branch history register of only the last three outcomes is needed. This PHT is illustrated below:

PHT Index | Corresponding Prediction
:--: | :--:
000(NNN) | X
001(NNT) | T
010(NTN) | X
011(NTT) | T
100(TNN) | T
101(TNT) | X
110(TTN) | N
111(TTT) | N

For example, whenever the previous three branch outcomes were NTT, the following outcome is always T. Therefore the PHT stores a T in the entry for 011 (NTT). While this table only uses T’s and N’s, a real predictor would use two-bit saturating counters and so each T-prediction in the example PHT above would really have a counter in either the WT or ST state.

If a history register of length 5 was used, then the PHT would have 32 entries, where only five of the entries were in use.

---

**P9.4 Suppose that most of the branches in a program only need a 6-bit global history predictor to be accurately predicted. What are the advantages and disadvantages to using a longer history length?**

Using a larger history register than necessary may increase the training time of the predictor. If a particular branch has an outcome that is always the opposite of the sixth previous branch, then the PHT must learn the patterns 1xxxxx0 and 0xxxxx1 (where x is either 0 or 1). Since there are six bits to the pattern, it may take 26 = 64 PHT entries to store the entire mapping. It takes time just to observe all of the patterns, and then it may require observing each pattern more than once to learn the corresponding prediction. Adding additional unnecessary history bits increases the number of patterns, and thus the training time. This in turn may lead to more mispredictions.





