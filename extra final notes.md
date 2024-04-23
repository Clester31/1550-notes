Here are more up to date and accurate notes that go over stuff based off topic
You can probabliy ignore the last bit of scheduling section since we probably won't be tested on it

# Last Bit of Scheduling

* Topics covered:
  * More scheduling algorithms
  * Mechanism v Policy
  * Real-time systems

## Shortest Process Next

* Pick process with the shortest estimated runtime
* Prioritizes CPU bound process since they will take less time to run
  * No need to worry about blocking in I/O bound processes
* long term may not be all that fair
  * Since CPU bound processes are preferred, longer-running process will get stuck in the back and may potentially never run
 
## Guaranteed Scheduling

* N processes get 1/N CPU time
* Sort jobs by accrued time and schedule the lowest one

## Fair Share Scheduling

* N users get 1/N of CPU time

## Lottery Scheduling

* Give each process tickets, winner gets chosen at random
* Higher priority process get more tickets than lower priority tickets
  * Although lower priority processes could be starved, overall is fair
 
## Mechanism vs Policy

* Mechanism is what we want to achieve
* Policy is how we go about achieving it
  * Example: we have a computer lab that closes at 8 PM
    * Policy: The computer lab closes at 8 PM
    * Mechanism: Security guards kick us out
   
## Real Time System

* A system that has processes which must be met at specific deadlines in order to run/work at full potential
* Soft: missing deadliens are bad but not fatal. May cause quality degredation
* Hard: missing a dealine results in system or critical failure

## How do we schedule with deadlines?

* If the workload is:
  * Known ahead of time
  * Does not change
  * Deadlines are able to be met
* Run earliest deadline first:
  * Prioritize work with sooner deadlines
 
# Inter Process Communication

* Topics Covered:
  * Race Conditions
  * Synchronization
  * Mutexes
  * Producer/consumer
  * Deadlock 

## What is a race condition?

* When a process or thread has:
  * A shared global state
  * Multiple (non-atomic) instruction operation
  * Where a state is read and then acted upon, and in between reading and acting upon the data, it could be changed from under us, rendering our action wrong
 
## Why do race conditions occur? What causes them?

* When multiple processes or threads have access to a shared global state and one process/thread changes something needed by the other process

## How are race conditions latent?

* We could run our code for a very long time and not notice a bug. It's only when we get interrupted mid-code that we see the bug

## What issues can be caused by race conditions?

* Deadlocks, starvation, unintended data modificiation

## What is a critical region? What purpose does it solve

* The critical region is a specific area in a piece of code that could contain variables or items that are susceptible to modifications from race conditions
* We want these regions to only be accessed by one process at a time

## Blame Preemption

* Blame preemption is when we disable interrupts within the critical region
* This approach undermines the role of the OS and can be potentially dangerous

## Synchronization Example

* we have process A that is doing work in the critical region
* If B tries to enter the critical region at the same time, it gets blockd,
* Then once A leaves, B can enter

## What is a mutex?

* A mutex is a region that is shared between processes. It can only be entered by a process when it is unlocked
* Once a process enters a mutex, it will lock the critical region.
* Once it has finished doing it's work, it will then unlock the mutex so another process can enter

## 4 Goals for synchronization

1. Only one process can be inside their critical region at the same time
2. No assumptions about CPUs or number of CPUs (should work on any system)
3. No process outside the critical region may block another process
4. No process should have to wait forever to enter its critical region

## Busy waiting

* While a process is waiting to exit its critical region / get preempted, we run a loop that does non-useful work in order to continue running while waiting for the other process to finish
* Prevents deadlock but is counter-productive. We could be using that time to do useful work

## Strict Alternation

* A synchronization technique where two threads take turns executing critical sections of code.
* Threads alternate between accessing the shared resource in a strict order
* We have some sort of turn variable that tells us whose turn it is
* Issue: a process could have it be their turn but they don't need to access the critical region
  * Other process will need to wait longer as a result
 
## Peterson's Solution

* Each process has a flag that indicates that it has interest in entering the critical region
* Then we indicate that it is our turn
* Employs busy waiting, only works for two processes

## Producer/Consumer problem

* Main idea: producer produces items into a buffer and the consumer consumes items from that buffer

### Producer

* If the buffer is full, we can no longer producer, go to sleep
* if not (or we wake up), add to our buffer and move the index
* increase our counter and if it is equal to 1, wakeup the consumer

### Consumer

* if our counter is empty, we have nothing to consumer, go to sleep
* if not (or we wake up), consume from our buffer and move the index
* decrease our counter and if its one less than our buffer size, wakeup the producer

## Condition Variables

* a variable of type pthread_cont_t represents a "condition" and acts someewhat like a phone number
* If a thread wants to sleep, it can invoke pthread_cond_wait() and go to sleep
* First parameter is a condition variable that enables another thread to "phone it" and wake it up
* Wake up with pthread_cond_signal

## up() and down()

* on down, we decrement our value cuont and if it is less than 0, we enqueue the current process and go to sleep
* on up, we increase our value count and if it is less than or equal to 0m we can dequeue and wake up our process

## Another deadlock example

![image](https://github.com/Clester31/1550-notes/assets/91839534/7258dc9c-3c29-4e1a-8d63-4063088842d1)\

* Deadlock 1 (producer): we reach semempty and the buffer is full. we then wait for the consumer to consume items.
  * However, the mutex is currently held by the producer, so the consumer will wait for the producer to give up the mutex, which will never happen and we deadlock
 
* Deadliock 2 (consumer): Consumer reaches semfull and the buffer is empty, so it waits on the producer to produce, but we one again are holding the mutex, so the producer cant get into the critical region. We deadlock

## Deadlock

* A set of processes is deadlocked if each process in the set is waiting for an event that only another process can cause

## Four events needed for deadlock to occur

1. Mutual exclusion (only one process can access the resource at a time)
2. Hold and wait (our process is holding a resource and waiting to get another one)
3. No preemption (we can not forcibly take a resource away from a process that is using it)
4. Circular wait (each process holds at least one resource neede by the hext process in the chain

## Four ways to deal with deadlock

1. Ignore
2. Detect and Recover
3. Avoid
4. Prevent (make one above always false)

## How to avoid deadlock?

* Impossible to perfectly prevent deadlock unless we know the future behavior of our programs, but if we really wanted we could just devolve into a batch scheduler which is already deadlock free

# Memory Management

Topics Covered:
* Degrees of multiprogramming
* Partitioning
* Protection/relocation problems
* Swapping
* Dynamic allocation
* Free space tracking with bitamaps and linked lists
* Overlays

## Why do we manage memory?

* Its limited, volatile, and we wish to share it

## Degree of multiprogramming

* The number of programs that can be kept in main memory at the same time
* A higher degree of multiprogramming means that we need to share RAM more often when we have more programs

## Fixed partitions

* We divide memory into fixed-size sections that processes can used.
* While this helps to protect against illegal memory acceses, it can also lead to to internal fragmentation

## Protection Problem

* When a process attempts to read or write to memory that belongs to another partition. This can lead to unauthorized access or datat corruption
* We can prevent this issue from happening by storing the extents of the partitons somewhere and checking all accesses against it

## Relocation problem

* Occurs when a process moves witin memory and another process tries to access it in the old location
* We can solve the relocation problem by turning absolute addresses into relative addresses by adding the base to them

## Issues with swapping

* Swapping is when we move code and data from RAM into disk
  * Creates the illusion of more memory
* if we swap the entire process memory, we lose the ability to run the program, effectively pausing it
* When we bring it back in, it may not be in the same location it started out in

## Dynamic allocation

* We want to give our programs extra space to grow in memory should they need it
* This hurts our degree of multiprogramming as we may have space that could go unused

## Free space tracking - bitmaps

* We need 1 bit per chunk to indicate whether we are free or in use
  * Magic number is usually 4kb as its small enough so that if we waste it isnt harmful but also easy to subdivide
* Increasing chunk size will ensure that a significant portion of our memory is not taken up by the free bit

## Free space tracking - Linked lists

* Using a sparse data structure like a linked list allows us to have a place to store our free or "interesting" entries, allowing the programmer an easy way to track what is free and what isn't

## Overlays

* Idea: memory is small, we want to have a way to make it seem like we have more memory than we have
  * VN doesnt say we need the entire program in RAM ahead of time, so just store it in disk 
* Overlays are dynamic hand written loading of subset of a program's code and data
* Divide programs into modules that are swapped in and out of memory as needed, load a module into memory when its needed for execution
* Essentially just moving to disk
* Issue: Tedious and error prone, must be done entirely by the programmer

# Virtual Memory and Paging

## Solving the virutalization of memory

* We want to establish a mapping between a virtual address and its physical address that:
  * preforms it's calculations in O(1) time
  * Does not require collisions to occur unless memory is full
  * The mapping object (page table) is small enough to fit in RAM, or preferrably on the processor chip itself
 
* We also want to automatically determine the portions of the virtual address space that must be currently loaded into memory

## Why do we have to virtualize memory?

* Memory is finite, we could have an instance where we run out of memory space for our pograms. We can use virtual memory as a way to choose a file that we need least and store it to disk, giving the user the illusion that they have more RAM than they do
* Virtual memory also allows for each process to have its own virtual address space, preventing an instnace where we could have an unauthorized memory access

## Page table

* Genereal idea: keep track of the mapping between virtual memory addresses and physical memory addresses. Each entry in the page table will correspond to a page in virtual memory along with its frame number in physical memory.
* Give the illusion of each process hainvg its own dedicated memory

## How to translate from virtual to physical

![image](https://github.com/Clester31/1550-notes/assets/91839534/4a16dcfc-4be0-40d2-8a0a-5f836f257707)

1. Look at the virtual address, and split it up into a page number and offset
  * Page number = VA / page size
  * offset = VA % page size
2. Next we need the to get the frame number which is just the page table indexed by the page number
  * page_table[page #]
3. Finally we calculate the physical address, which is the frame number combined with the offset
  * PA <- Frame # * Page Size + offset

## Time complexity of a page table

* Work done in our page table should be in O(1) time, but operations such as multiplication and division can be done by simply bit shifting

## What is in each page table entry

* A page table entry is composed of three different segments:
   * Protection bits: these are bits that specifiy whether or not we can read/write/execute code/data in that page
     * Marking a stack page as no-execute mitigates buffer overrun vunerabilities (just like in mmap)
   * D/R/V bits:
     * Dirty bit: 1 if a page has been modified since loaded
     * Reference bit: 1 if a page has been used in a page to frame translation
     * valid: 1 if this PTE represents a page in a frame or if this is just taking up space in our page table
   * Page frame number
 
## Multilevel page tables

* Issue: although we have chunked our virtual address space into pages, our table is still too large on a per-process basis
  * We notice that valid entries are quite rare if we assume that RAM is smaller than the address space demands of every process on our system
  * What we should do is try to represent our page table as a sparse data structure: One where we only need to keep track of the valid entries
* Multilevel page tables provide a solution to this issue. They can be represented as trees with fixed depth
  * This means our worst case will only be the depth of the tree. 
 
### Structure of a multi-level page table

![image](https://github.com/Clester31/1550-notes/assets/91839534/d6e6f1c9-39e1-4b97-86f7-3559d092ed76)

* We have a root that is always present and points to leaves
* Each leaf contains multiple PTEs that store the page to frame mapping
* How is this sparse?:
  * If we have an entire leaf that only contains invaild PTE's, we can just omit it.
 
### MLPT translation

1. Get our page number by doing VA / page size
2. Split up the page number into two parts
  * The first half will bee the index into our first level page table
    * The first level frame will point to its entry point into the second level page tale 
  * The second half will be the index into the second level page table
3. We then use the frame number from the second page table as the first part of our physical address
4. Add the offset

## Inverted Page Tables

* Issue: we have vastly more pages than there are frames in a typical system
* Solution: Lets build a page table that is indexed by the frame number that will tell us what is in that frame
* Called inverted since its the reverse of a typical page table
* Now we could say store in index 1 that page 7 lives in frame 1
  * Issue: every process is now going to have a page 7
  * How do we solve this, express whose page 7 it is by using its process ID
  * This means we only need one inverted page table per system
 
### Inverted page table translation

![image](https://github.com/Clester31/1550-notes/assets/91839534/802f79f8-7227-4346-b365-dd5c574a8368)

1. We still have the same VA structure as before, but now we must take into acount the process id
2. Linear search through the inverted table
  * Our key is (PiD, PageNum)
  * our index is the frame number
3. Acquire the frame number and combine it with offset to get our physical address

###







