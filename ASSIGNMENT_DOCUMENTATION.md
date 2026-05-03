# Assignment 3 - Complete Documentation

**Student Name**: [Meshal Alharbi]  
**Student ID**: [445050105]  
**Date Submitted**: [2026/5/2]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [(https://drive.google.com/file/d/19ezMcM6XccyaubXopq5Fw9yb4GOe2Ior/view?usp=sharing)]

**Video filename**: `[445050105]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [May 1, 4:00 pm]

**What I implemented**: 
project setup and student identification.

**Challenges encountered**: 
locating the correct file for identification.

**How I solved it**: 
added my student ID and name to the documentation and main file headers

**Testing approach**: 
clean build and compilation check

**Time spent**: 
30 min

---

### Entry 2 - [May 2, 6:15 pm]

**What I implemented**: 
Integrated full synchronization by adding imports, implementing ReentrantLock for shared resources, and adding Semaphore(1) logic for CPU control.

**Challenges encountered**: 
Coordinating between protecting data (counters) and managing thread execution (CPU access) within a short timeframe.

**How I solved it**: 
Applied fine-grained locking for counters and wrapped the entire process execution in a try-finally block for the semaphore.

**Testing approach**: 
Console output analysis to verify that only one process executes at a time without data corruption.

**Time spent**: 
2:00 hours

---

### Entry 3 - [May 2,8:50 pm]

**What I implemented**: 
Final testing, verification, and completing the documentation.

**Challenges encountered**: 
Verifying consistency across multiple runs.

**How I solved it**: 
Ran the simulation 5 times and confirmed that the outputs matched the expected values.

**Testing approach**: 
Stress testing by running the simulation multiple times and comparing results for stability.

**Time spent**: 
40 min

---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:

- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

- What shared resource is affected?
  
  Race Condation #1: The counter variable contextSwitchCount in the SharedResources class.
  
  Race Condition #2: The executionLog (which is an ArrayList) in the SharedResources class.

- Why is concurrent access a problem?
  
 Race Condition #1: The increment operation (++) is not atomic; it involves three steps (read, modify, write). Multiple threads can read the same value before any of them writes the update.

 Race Condition #2: ArrayList is not thread-safe. When multiple threads call .add() at the same time, they may try to write to the same index in the underlying array.

- What incorrect behavior could occur?
  
  Race Condition #1: "Lost updates" occur, where the final number of context switches displayed is lower than the actual number of times they happened.
  
  Race Condition #2: This can lead to a ConcurrentModificationException causing the program to crash, or it can cause data corruption where log entries overwrite each other or disappear.

[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

The Difference:
ReentrantLock is a mutual exclusion (mutex) tool used to protect shared data by allowing only one thread at a time. A Semaphore is a signaling tool that uses permits to limit how many threads can access a resource simultaneously.

Implementation:
Locks: I used them in SharedResources to protect the counters and execution log, ensuring exclusive access to prevent race conditions.

Semaphores: 
I used a Semaphore(1) in the Process class to simulate a single-core CPU, ensuring only one process thread executes at any given moment.

[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

Deadlock is a situation where two or more threads are blocked forever, each waiting for a resource held by another. To prevent this, I used try-finally blocks and Lock Ordering. Specifically, I wrapped every lock() and acquire() call within a try block, ensuring the corresponding unlock() or release() is executed in the finally block. This ensures that resources are always released even if an exception occurs, preventing any thread from holding a resource indefinitely and causing a system stall.

[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

I chose fine-grained locking by using separate ReentrantLock objects for each counter.
I made this choice because the counters (context switches, completed processes, and burst time) are logically independent, so updating one shouldn't block the others.
The trade-off is that while fine-grained locking is slightly more complex to implement than a single coarse-grained lock, it significantly reduces thread contention. 
Since the resources are independent, the fine-grained approach provides better concurrency because it allows multiple threads to update different counters simultaneously, maximizing system throughput and performance.

[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
The shared counter variables in the SharedResources class: contextSwitchCount, completedProcessCount, and totalWaitingTime.
**Why they need protection**: 

These variables are accessed and modified by multiple process threads simultaneously. Operations like ++ or += are not atomic (read-modify-write), so without protection, threads could overwrite each other's updates, leading to "Lost Updates" and incorrect simulation statistics.

**Synchronization mechanism used**: 
I implemented Fine-Grained Locking by using a separate ReentrantLock for each individual counter (e.g., contextSwitchLock, completedProcessLock, and waitingTimeLock).

**Code snippet**:
```java
// public static void incrementContextSwitch() {
        // TODO: Protect this critical section with a lock
			contextSwitchLock.lock();
    	try {
            contextSwitchCount++;
    	}
    	finally {
    		contextSwitchLock.unlock();
    	}
    }

```

**Justification**: 
This approach maximizes concurrency by allowing independent counters to be updated simultaneously without thread contention, ensuring performance and data integrity.

---

### Critical Section #2: Execution Log

**What resource**: 
The shared ArrayList<String> named executionLog located in the SharedResources class.

**Why it needs protection**: 
The ArrayList class is not thread-safe. If multiple processes try to add log entries simultaneously, it can lead to data corruption, lost logs, or throw a ConcurrentModificationException, which would cause the simulation to crash.

**Synchronization mechanism used**: 
A ReentrantLock named logLock to ensure mutual exclusion (only one thread can write to the log at a time).
Code snippet:

**Code snippet**:
```java
// From your SharedResources class
public static final ReentrantLock logLock = new ReentrantLock();

public static void logExecution(String message) {
    logLock.lock(); // Added to ensure thread safety
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}
```

**Justification**: 
It prevents ConcurrentModificationException and ensures that the shared list is updated in a thread-safe manner, preserving the correct chronological order of events.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
To manage the "CPU" as a limited shared resource. It ensures mutual exclusion during the execution phase, preventing multiple processes from running their time quantum simultaneously in a simulated single-core environment.

**Number of permits and why**: 
1 permit. I used a single permit because the simulation is designed to mimic a single-core processor, which can only handle one execution thread at any given moment.

**Where implemented**: 
Defined as a static variable in the SharedResources class and implemented within the run() method of the Process class to wrap the quantum execution logic.

**Code snippet**:
```java
// From your Process class run() method
try {
    SharedResources.cpuSemaphore.acquire(); // Acquire the CPU
    try {
        if (startTime == -1) {
            startTime = System.currentTimeMillis();
        }
        // ... (quantum execution logic) ...
        int runTime = Math.min(timeQuantum, remainingTime);
        // ...
    } finally {
        SharedResources.cpuSemaphore.release(); // Release the CPU
    }
} catch(InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

**Effect on program behavior**: 
The semaphore forces serialized execution. Without it, multiple threads would print their progress bars and logs at the same time, causing interleaved and messy terminal output. With the semaphore, the program clearly shows one process executing its quantum to completion (or yield) before the next one starts, ensuring a logically correct CPU scheduling simulation.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash 
# 1. Compile the Java source code
javac SchedulerSimulationSync.java

# 2. Execute the program 5 consecutive times to verify consistency
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
"After running the simulation 5 consecutive times, the Total Completed Processes remained constant at 17 (based on my student ID seed 445050105), and no data corruption or crashes were observed."

**Why synchronization is necessary**: 
"Synchronization is necessary because shared variables like contextSwitchCount and completedProcessCount are accessed by multiple threads simultaneously. Without locks, a Race Condition would occur where two threads try to update the same counter at once, causing 'Lost Updates.' This would lead to incorrect final statistics, even if the program appears to run normally."

**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 

**Results**: 

**What this proves**: 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/Meshal-Alharbi-445/OS-Assignment3-Meshal-Alharbi.git

**Number of commits**: 6

**Commit messages**: 
1. Set my student ID:445050105
2. ReentrantLocks to protect shared counters (contextSwitcCount, completedProcessCount, totalWaitingTime)
3. Implemented Semaphore for CPU access control
4. Implemented thread-safety for all shared counters and execution log using ReentrantLocks
5.Added CPU semaphore acquisition and release in Process run method to ensure controlled access for concurrent execution.
6.Fix race condition in logExecution by adding missing lock

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
