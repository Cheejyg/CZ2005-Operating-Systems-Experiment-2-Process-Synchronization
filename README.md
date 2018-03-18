# Experiment 2

## Process Synchronization

### 1. OBJECTIVES

After completing this lab, you will be able to: 

* Understand how to synchronize processes/threads.
* Understand interleavings and race conditions, and master some way of controlling them.
* Know how to use _locks/semaphores_ to solve a critical section problem.

### 2. LABORATORY

**Software Lab 1B** (N4-01a-02)

### 3. EQUIPMENT

Pentium IV PC with Nachos 3.4.

### 4. MODE OF WORKING

You should be working alone. No group effort.

### 5. ASSESSMENT

The assessment of this lab will be based on oral examination.

### 6. THREAD OPERATIONS

The thread is a light-weight process in Nachos. In the first experiment, we have learnt the basic feature of thread execution in Nachos. In this experiment, we need to use the following thread operations to finish the exercises. Please read the source code under the directory (particularly for thread.h and thread.cc).

```
void Fork(VoidFunctionPtr func, int arg, int joinP);
// Make thread run (*func)(arg) and
 // know if a Join is going to happen.
```

`Fork` creates a new thread of control executing in the calling user thread address space. The function argument is a procedure pointer. The new thread will begin executing in the same address space as the thread calling `Fork`. The new thread must have its own user stack in the user address space, separate from all other threads. It must be able to make system calls and block independently of other threads in the same thread.

```
void Join(Thread *forked);     // Waits for specified thread to
                                // finish before continuing
```
A parent thread may call Join to wait for a child thread to complete (e.g., to Exit).

```
void Yield();                 // Relinquish the CPU if any
                                    // other thread is runnable
```

A thread gives up the CPU ownership and allows another thread to execute. Its state shifts from running to ready, and it is put to the ready queue.

```
void Sleep();                 // Put the thread to sleep and
                                    // relinquish the processor.
```

A thread gives up the CPU ownership and allows another thread to execute. Its state shifts from running to waiting (waiting for the wake-up event). When the wake-up event is triggered, the thread is put to the ready queue.

### 7. CONTEXT SWITHCHES

On a multiprocessor, the executions of threads running on different processors may be arbitrarily interleaved, and proper synchronization is even more important. In Nachos, which is uniprocessor-based, interleavings are determined by the timing of context switches from one thread to another. On a uniprocessor, properly synchronized code should work no matter when and in what order the system chooses to run the threads. The best way to find out if your code is “properly synchronized” is to see if it breaks when you run it repeatedly in a way that exhaustively forces all possible interleavings to occur. This is the basic idea to check whether multiple threads have the problem of race condition. To experiment with different interleavings, you must somehow control when the executing program makes context switches.

Context switches can be either voluntary or involuntary. Voluntary context switches occur when the thread that is running explicitly calls a yield or sleep primitive (Thread::Yield or Thread::Sleep) to cause the system to switch to another thread. A thread running in Nachos might initiate a voluntary switch for any of a number of reasons, perhaps in the implementation of a synchronization facility such as a semaphore.

In contrast, involuntary context switches occur when the inner Nachos modules (Machine and Thread) decide to switch to another thread all by themselves. In a real system, this might happen when a timer interrupt signals that the current thread is hogging the CPU.

### 8. EXERCISES

We have two exercises in this experiment.

1. In this exercise, we will conduct the following steps to understand race condition problem in Nachos.

	1) Change your working directory to lab2 by typing `cd ~/nachos-3.4/lab2`
	
	2) Read the Nachos thread test program ___threadtest.cc___ carefully. There is a shared variable named ___value___ (initially zero). There are two functions, namely void ___Inc(\_int which)___ and void ___Dec(\_int which)___ , where increases and decreases ___value___ by one, respectively. In this exercise, you need to consider different interleaving executions of _Inc_ and _Dec_ so that the shared variable value is equal to a predefined value after threads complete.
	
	3) You need to implement the following three functions. When all the threads (two _Inc\_v1_ threads and two _Dec\_v1_ threads) complete in _TestValueOne()_, value=1.
		
		```
        void Inc_v1(_int which)
        void Dec_v1(_int which)
        void TestValueOne()
		```
	
		In _Inc\_v1_ and _Dec\_v1_, you need to use _Yield_ primitive in Nachos to induce context switch. ___Inc\_v1 and Dec\_v1 should have the same logic as Inc and Dec, respectively. You are only allowed to add Yield into those two functions.___ You need to implement _ThreadValueOne()_ by creating two threads with _Inc\_v1_ and two threads with _Dec\_v1_. The current thread should wait for all those threads to complete. At the end of _TestValueOne()_, a checking is performed on whether the value is _1_. If the checking is passed, you should get the message "congratulations! passed.". Otherwise, an error message is printed.
	
	4) After you finish implementing the above-mentioned functions, you can demonstrate the result of _TestValueOne()_, by commenting other test functions in _ThreadTest()_ like below.
		
		```
        //for exercise 1.
        TestValueOne();
        //TestValueMinusOne();
        //for exercise 2.
        //TestConsistency();
		```
	
	5) Compile Nachos by typing make. If you see "_ln -sf arch/intel-i386-linux/bin/nachos nachos_" at the end of the compiling output, your compilation is successful. If you encounter any anomalies, type `make clean` to remove all object and executable files and then type `make` again for a clean compilation.
	
	6) Test this program by typing `./nachos`. If you see “congratulations! passed.” at the end of the debugging messages, your program is successful. Otherwise, “failed.” will be displayed.
	
	7) Repeat Steps 3)–6), and implement the following three functions. When all the threads (two _Inc\_v2_ threads and two _Dec\_v2_ threads) complete in _TestValueMinusOne()_, value=-1. At Step 4), you need to test _TestValueMinusOne()_.
		
		```
        void Inc_v2(_int which)
        void Dec_v2(_int which)
        void TestValueMinusOne()
		```
	
2. In this exercise, we will conduct the following steps to understand process synchronization problem in Nachos.
	
	1) Change your working directory to lab2 by typing `cd ~/nachos-3.4/lab2`
	
	2) You need to implement the following three functions. When all the four threads (two _Inc\_Consistent_ threads and two _Dec\_Consistent_ threads) complete in _TestConsistency()_, ___value=0___. You need to achieve consistent result (___value=0___), regardless of different interleaving execution orders in _Inc\_Consistent_ and _Dec\_Consistent_ as well as different thread fork orders in _TestConsistency()_.
		
		```
        void Inc_Consistent (_int which)
        void Dec_Consistent (_int which)
        void TestConsistency ()
		```
		
		In _Inc\_Consistent_ and _Dec\_Consistent_, you use _Yield_ interface in Nachos to induce context switch. You need to implement _TestConsistency()_ by creating two threads with _Inc\_Consistent_ and two threads with _Dec\_Consistent_. The current thread should wait for all those threads to complete. At the end of _TestConsistency()_, a checking is performed on whether the ___value___ is 0. If the checking is passed, you should get the message "congratulations! passed.". Otherwise, an error message is printed.
	
	3) After you finish implementing the above-mentioned functions, you can demonstrate the result of _TestConsistency()_, by commenting other test functions in _ThreadTest()_ like below.
		
		```
        //for exercise 1.
        //TestValueOne();
        //TestValueMinusOne();
        //for exercise 2.
        TestConsistency();
		```
	
	4) Compile Nachos by typing `make`. If you see "_ln -sf arch/intel-i386-linux/bin/nachos nachos_" at the end of the compiling output, your compilation is successful. If you encounter any anomalies, type `make clean` to remove all object and executable files and then type `make` again for a clean compilation.
	
	5) Test this program by typing `./nachos`. If you see “congratulations! passed.” at the end of the debugging messages, your program is successful. Otherwise, “failed.” will be displayed. In the oral exam, you need to demonstrate your testing with different interleaving execution orders in _Inc\_Consistent_ and _Dec\_Consistent_ as well as different thread fork orders in _TestConsistency()_.
