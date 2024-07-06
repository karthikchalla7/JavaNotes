## MemoryManagement

**Two types of Memory(RAM)**
1. Stack
2. Heap

* Both Stack and Heap are created by JVM and stored in RAM

**Stack Memory**
- Stores temporary variables and sepreate memory blocks for methods
- Stores primitive data types
- Stores reference variables of the heap objects
  1. strong reference
     - ex: Object obj = new Object(); strong reference
  2. weak reference
     - As soon as GC runs it will remove the obj
  3. soft reference
     - It allows to remove the object by the GC if the space is not present.
     - Totally this depends on the space in heap.
- Ex: Object reference = new Object(); reference - stored in stack
- Each thread has its own stack memory (Due to this we can have multiple stacks) but they share only one heap memory
- Variables/Methods within a SCOPE is only visible and as soon as variable/method goes out of scope it gets deleted from the stack(in LIFO order)
- When stack memory goes full, it throws "java.lang.StackOverflowError"
- String pools are stored in heap so any stringliterals are stored in heap whereas as the reference varaibles are stored in stack.
- But when you create string using new keyword then it creates a new memmory for storing in heap.


**Heap Memory**
- Stores objects
- There is no order for allocating the memory
- Garbage collector is used to delete the unreferenced objects from the heap.
- Garbage collector ran by JVM to remove the obejct which doesn't have any object references. JVM will have total control over freeing up the heap memory.
 - Mark and Sweep Algorithm
   - Marks the objects which doesn't have any references it marks since their is no reference
   - Then sweep comes and removes the marked objects and moves the remaining to survior space(s0,s1) along with age.
   - This way we can keep eden and the subsequent surivor space for objects to come.
   - We can give threshold for the objects based on age let say if age is 3 then move those objects to old generation.
 - Types of GC:
   - Serial GC
     - Only one thread is working.
     - Disadavantages are very slow and GC is very expensive
     - Whenever GC starts all the application threads will get paused
     - To tackle this we have parallel GC
   - parallel GC
     - Default in java 8
     - Multiple threads are working parllely
   - CMS(concurrent mark sweep)
     - Further improve parallel gc there comes CMS
     - Application threads will not stop but it is not for sure
     - no memory compaction happens but pausing still remains.
   - G1
     - The better version of CMS
     - This brings the compaction all the freed up memory is brought to back. With less pause time
- Heap memory is shared with all the threads
- Heap also contains string pool
- When heap memory goes full,it throws "java.lang.OutofMemoryError"
- Heap memory is further divided into:
  - Young Generation(minor GC happens here)
    - Eden
    - Survivor Space(S0,S1)
  - Old/Tenured Generation(major GC happens here)
  - Permanent Generation changed to (non heap - meta space)

**Garbage Collector Algo**
- Mark and Sweep and Compact memory
- Similar to mark and sweep but difference comes with compact
- So after removing their will be spaces in between so this compact will align the remaining object in order and makes spaces in the end.
- The world stops when the Garbage Collector runs

