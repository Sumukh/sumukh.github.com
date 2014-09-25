



CS194-15 Assignment 1
===================
Sumukh Sridhara  
-----

## (cs194-ad)
September 16th 2014. 


Measuring CPU Performance  

[TOC]

----------



Question 1
===========

1.3.1 - Results
-------
With Printing: 

> With O2 - Cycles to compute the sum = 49995000
> 
>     hive15 [38] g++ -msse4 -O2 counters.cpp sum.cpp -o sum
>     hive15 [39] ./sum 10000
>     Sum is 49995000
>     Steps taken 21437
> 
> Without O2 - Cycles to compute the sum =  54191704
> 
>     hive15 [40] ~/hw1 # g++ -msse4 counters.cpp sum.cpp -o sum
>     hive15 [41] ~/hw1 # ./sum 10000
>     Sum is 54191704
>     Steps taken 62884
> 
> > **Comment:** The sum is incorrect.

------
Without Printing: 

> With O2 - Cycles to compute the sum = 1401
> 
>     hive15 [43] ~/hw1 # g++ -msse4 -O2 counters.cpp sum.cpp -o sum
>     hive15 [44] ~/hw1 # ./sum 10000
>     Steps taken 1401
> 
> > **Comment:** It seems to avoid the naive computation.
> 
> Without O2 - Cycles to compute the sum =  62899
> 
>     hive15 [45] ~/hw1 # g++ -msse4 counters.cpp sum.cpp -o sum
>     hive15 [46] ~/hw1 # ./sum 10000
>     Steps taken 62899


------
Hardcoding loop iteration: 
Avoided user input in the loop and set it to 10000 

> With O2 (and no printing) Cycles to compute the sum = 1475
> 
>     hive15 [50] ~/hw1 # g++ -msse4 -O2 counters.cpp sum.cpp -o sum
>     hive15 [51] ~/hw1 # ./sum
>     Steps taken 1475
> 
> Without O2 (and no printing)  - Cycles to compute the sum =  66772
>
>     hive15 [48] ~/hw1 # g++ -msse4 counters.cpp sum.cpp -o sum 
>     hive15 [49] ~/hw1 # ./sum 
>     Steps taken 66772
> 
> With O2 (but with printing) - 1376
> 
>     hive15 [53] ~/hw1 # g++ -msse4 -O2 counters.cpp sum.cpp -o sum
>     hive15 [54] ~/hw1 # ./sum
>     Sum is 49995000
>     Steps taken 1376
> Without O2 (but with printing) - 66843
> 
>     hive15 [57] ~/hw1 # g++ -msse4 -O2 counters.cpp sum.cpp -o sum
>     hive15 [58] ~/hw1 # ./sum
>     Sum is 49995000
>     Steps taken 66843

-----

1.3.2 - Timing (2 points) 
--------
Time all eight configurations (see all the above ones) and explain the discrepancies. 

	1st test: 
		Running with O2 (but with printing & user input): The compiler performs optimizations to the rather naive code to enable a 3x speedup. 

		Without O2 - the code runs naively with no optimzations. 

	Without the print statements: 
		1. When I ran it with 02 - one of the compiler optimizations must have been to realize that it didn't need to to compute the sum - so it avoided doing that calculation naively. It may have unrolled the loop or used more advanced optimizations. 
		2. Without 02 - It ran in about the same time (+/- 15 cycles) as normal execution with printing. 

	Without user input (hardcoding)
		1. With 02 (and no printing) - this ran in about the same time (+/- 5%) as O2 with user input. This is because the optimizations that speed up the loop in the first place. By not having to print the value, it might be able to even avoid doing the computation. 

		2. Without O2 (and no printing of the variable) - this actually was about 6% longer - not a huge difference but it looks like not printing didn't help because there weren't any optimizations made(the print statement only runs once anyway).

		3. With O2 & With printing - This was the fastest of all the tests. By hard coding the value of the loop the O2 can unroll the loop and include the optimzations. 

		4. Without O2 & With printing - This took as long as the normal code without O2. Hardcoding the value didn't save that much computation because most of the work is the for loop.

1.3.3 - Accuracy (2 points) 
---------

Which configuration is the most accurate? 

This configuration - O2 With Printing and Without User Input. (and hard-coding). This is also the fastest. 


1.3.4 - atoi + argv (2 points) 
---------

What is `argv[1]`?  

 `argv` is the array that holds the command line arguments. `argv[1]` is the actual parameter (which is `10000` in this case). (BTW - index[`0`] is the entire command). 

What is `atoi()`?

 This is a function that takes in a string and coverts it to an integer. When we combine the two  - `atoi(argv[1])` - returns the integer value of the parameter. 


# Question 2 - Memory Latency

2.3.1 - Pointer Chasing Explanation (2 points) 
---------
How does pointer chasing measure memory latency?: 

It measures it trying to access random locations in the array - essentially jumping between places in the array. Through the random accesses to different parts of the array - we invalidate the benefit of caching and have to read from memory almost every time. This for loop really tests the time it takes to do the reads from memory.

   	for (int k = 0; k < 1048576; k++ ) {
   		j = randNum[j];
   	}

2.3.2 - int array size (1 point) 
------
How many bytes is an int array of length N? 

sizeof(int) is 4bytes so an int array of length N is 4N bytes

2.3.3 - Graph (32 Points) 
--------
![Graph](https://docs.google.com/a/berkeley.edu/spreadsheets/d/1FoZMzr9YUPaKLpPbYVAaLKtDn-xAc-B6iOtA9jMj-pM/embed/oimg?id=1FoZMzr9YUPaKLpPbYVAaLKtDn-xAc-B6iOtA9jMj-pM&oid=185735040&zx=yv8zno8071qb "Graph")

http://docs.google.com/a/berkeley.edu/spreadsheets/d/1FoZMzr9YUPaKLpPbYVAaLKtDn-xAc-B6iOtA9jMj-pM/gviz/chartiframe?oid=185735040

Re: Graph - See Attached JPG Above. 


2.3.4 - Change in the curve 
--------
Explain change in shape of the curve: 

As the array size increases, the ability of the to cache the array decreases. Intially a good portion fits lower on the cache hierarchy (aka closer to the cpu) - however as the size grows it rapidly loses this advantage (the slope increases rapidly). It then moves onto the next cache level - which allows it to fit a larger size cache and the slope decreases as its on the same level. When it then starts to use the entirety of the cache the slope is relatively low. This process repeats as the size of the array grows (as result of moving between levels of caches) - though eventually you grow out of space on caches and you need to go to DRAM more and more often. 

2.3.5 - Pipeline (2 points)
---------------
Pipelining & OOO affecting our benchmark: 

    This doesn't affect our benchmark because we introduce a "data hazard". We force the instructions to wait until we've fetched the data from memory. Only then can the next instruction know the location from memory that it should fetch from since there is a data dependency. This dependency prevents the CPU from running multiple instructions or heavily pipelining. 

Bonus : **TODO**

# Part 3

3.4.1 Measuring Memory Bandwidth 
------

1. Plot:
![Graph](https://docs.google.com/a/berkeley.edu/spreadsheets/d/1FoZMzr9YUPaKLpPbYVAaLKtDn-xAc-B6iOtA9jMj-pM/embed/oimg?id=1FoZMzr9YUPaKLpPbYVAaLKtDn-xAc-B6iOtA9jMj-pM&oid=1660941341&zx=ehog55415xf7 "Graph")
X Axis is Log Scale / Kilobytes
Y Axis is in Mbps 

See above PNG
Also: http://docs.google.com/a/berkeley.edu/spreadsheets/d/1FoZMzr9YUPaKLpPbYVAaLKtDn-xAc-B6iOtA9jMj-pM/gviz/chartiframe?oid=1660941341

3.4.2 Shape of the Curve (4 points) 
----------
The two simd copy methods perform better than the naive implementation.  The naive implementation experiences a sudden increase in bandwidth between 1024KB and 2048KB. I imagine because the naive algorithm increase is because it can start effectively caching on L3 and higher caches  - but this could be a fluke. As for other increases, SIMD Mem Cache experiences increases near the beginning and before 1000KB. This seems to be the result of taking better advantages of caches. The overall pattern is for bandwidth to decrease - but for small periods the code can actually take advantage of the cache (not fill it up) so it will become faster - but after a while it loses that benefit and the code  slows down. 

3.4.2 Warming up the cache (2 Points)
----------
Why do we need to warm up the cache: 
We want an equitable test - and not the test cache invalidation and make sure what was in the cache before does not affect our results. It hypothetically could affect the other algorithms if they also need to invalidate the previous ones results.  

3.4.3 Efficiency & Accuracy (2 Points)
----------
An inefficient copy would be bound by software bottlenecks and not memory bottlenecks. This would imply a slow implementation would result in the test revealing a slow memory bandwidth - it would take longer than it should. The software should be as fast as possible to isolate the memory bandwidth as the only bottleneck - because we're trying to measure memory. (If it's CPU bound - all memory would perform very similiarly) 

3.4.3 Intrinsics (2 x 3 Points)
----------

Intrinsics Calls: 

`_mm_prefetch` -  It tells the CPU to fetch the data at a memory address into a specific place in the caching heirarchy - but in this case it's hinting towards Non-temporal accesss - this helps it load a bunch of data without filling up the caches. 

`_mm_load_si128` - This loads 128bit integer from memory. 

`_mm_stream_si128` - This stores a 128bit integer (2nd param) into a 16bit aligned memory address (1st param) except this doesn't follow the normal caching rules (so it avoids caching)

`_mm_store_si128`  - This stores a 128bit integer (2nd param) into a 16bit aligned memory address (1st param)

Sources: 
https://software.intel.com/sites/landingpage/IntrinsicsGuide/
http://stackoverflow.com/questions/37070/what-is-the-meaning-of-non-temporal-memory-accesses-in-x86
http://lwn.net/Articles/255364/

3.4.4 Bonus
------
Here's the outline of code required to detect the amount of unique walks. Basically we implement cycle detection by walking through the array and we measure the amount of steps we can get through before running into an index we've seen before. We then return the counter (the loop iteration variable) 
```C
int unique_vals(int *orig, int length) {
	int seen[length];
	int x = 0;
	int y = 0;
	for (int i = 0; i < length; i++ ){
		seen[i] = -1; 
	}

	for (int i = 0; i < length; i++) {
		y = x; 
		x = orig[x]; 
		if (seen[x] == 1) {
			return i; 
		} 
		seen[y] == 1;
	}
	//We got through the entire list. 
	return length;
}
```


----

# Part 4

4.3.1 Flops/IPC Table (6 * 4 Points) 
------
Multiplying matrices of size $2^{10}$ (1024) 
IPC = Instructions per cycle. 

**Results:** 

| Name	  | Gigaflops | IPC   |
| :------- | ----: | :---: |
| opt_simd_sgemm    | 9.001861   |  2.350218   |
| opt_scalar1_sgemm   | 1.098515 |  2.596334  
| opt_scalar0_sgemm   | 0.888449 |  0.731702  |
| naive_sgemm | 0.212952 |  0.328017  |

4.3.2 How many Flops(s)? (3 Points) 
------
In terms of $n$ the size of the two square matrices - we can represent the number of flops required to multiply those two as $2*n^3$. There are $n^2$ elements in each array and we need to do $2n-1$ additions (naive matrix multiplication has additions). This makes the entire function perform approximately $2n^3$ operations. 

4.3.3 IPC > 1 (2 Points) 
------
It's not actually computing more than one instruction per cycle but rather using the full power of the CPU to execute multiple instructions in one cycle using native optimizations like Intel intrinsics, simd, sse etc. By using these optimizations to compute multiple things at once (again using native CPU optimizations), more work can be done in a cycle and the IPC count will appear to be > 1.  


4.3.3 Flops vs. IPC (2 Points) 
------
Despite having a lower IPC, SIMD is performing more work because it is more tuned towards doing more/quicker floating point operations. (Optimized on the hardware). SIMD bunches up the operations it does into one instruction so it might seem like a similar or lower IPC (aka Single Instruction Multiple Data)   - but it's doing more work that scalar1. A higher IPC is not necessarily an indicator of efficiency - we don't know what the instructions are. We could do a bunch of optimized but useless calculations to increase IPC - but not actually speed up the program. 


> Sumukh Sridhara



























