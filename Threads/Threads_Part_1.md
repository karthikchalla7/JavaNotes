
## Threads Process and their memory model

### Process:

- Process is an instance of a program that is getting executed.
- It has its own resource like memory,thread etc. OS allocate these resouces to process when its creatad.
  - Lets take a example test.java
  - Compilation (javac test.java) : generates bytecode that can be executed by JVM
  - Exceution(java test): at this point JVM starts the new ***process***.
- Each process have their own resources. They can run in parallel.
- Process takes care of execution of the program.
  
### Thread:

- Thread is known as lightweight ***process***.
- Smallest sequence of instructions that are executed by CPU independently
- And one process can have multiple threads
- When a process is created it starts with one thread and that initial thread known as ***main thread***
- From that we can create multiple threads to perform task concurrently.
   
### Process and Threads:

![image](https://github.com/karthikchalla7/JavaNotes/assets/76682351/2eb196d7-e874-46f9-b9e0-bf58941cf307)

- JVM contains heap,stack,code segment,data segment,registers,program counters..memories
- Heap and Stack where garbage collectors works.
- When we are executing a program
  - It will create a process
  - A new JVM instance is created
  - Each instance will have all types of memories
- In a process we can have multiple threads.
- Register,Stack,Counter are local to each thread.

#### Code Segment
- Contains the compiled bytecode(i.e machine code) of the java program.
- It is read only.
- Complete machine code is present here.
- All threads within the same process, share the same code segment.

#### Data Segment
- Contains the Global and Static Variables.
- All threads within in the same process, share the same data segment
- Threads can read and modify the same data.
- Synchronization is required between multiple threads.

#### Heap
- Objects created at runtime using "new" keyword  are allocated in the heap.
- Heap is shared among all the threads of the same process(but NOT WITHIN PROCESS)
- Lets say in process1 X8000 heap memory pointing to some location in physical memory, same X8000 heap memory points to different location for process2.
- Threads can read and modify the heap data.
- Synchronization is required between multiple threads.

#### Stack
- Each thread has its own stack
- It manages,method call, local variables.

#### Register
- When JIT(Just in time) compiles converts the Bytecode into native machine code,its uses register to optimized the generated machine code.
- Also helps in context switching.
- Each thread has its own register.

#### Counter
- Also known as program counter, it points to the instruction which is getting exectued.
- Increments its counter after successfully exectuion of the instruction.
- Like a book keeper which instruction need to run by specific thread.

- OS Scheduler manages the threads or JVM Scheduler.
- OS it self manages the JVM scheduler.
- Lets say only one CPU and one register like one core.
- we have three threads now they have to run.
- Lets say we run the main thread and load the machine code into register.
- Register can stroe intermediate results/data into their respective thread registers.
- Register is like a vitual memory.
- Register helps in context switching.
- Progarm counter provides where a particular thread start its exectuion and it points to the address of the machine code needs to be loaded.
- Since only one core we can preempt the thread and load another thread.
- Then we can prempt again and load the another thread and store all the execution/intermediate data in registers.
- So when the previous thread again comes into the execution the CPU starts from where that thread has left.
- This type of switching and continuing is called context switching.



***All are managed by JVM***

### How much memory does process gets?
- while creating the process "java MainClassName" command, a new JVM instance will get created and we can tell how much heap memory need to be allocated.
- java -Xms256m-Xmxg8 MainClassName
- Xms<size>: This will set the initial heap size, above, I allocaed 256mb
- Xmx<size>: Max heap size, process can get, above, i allocated 2GB, if tries to allocate more memory, "OutOfMemoryError" will occur.
  

### Defintion of Multithreading:
- Allows a program to perfrom multiple tasks at the same time.
- Multiple threads share the same resource such as memory space but still can perfrom task independently.

### Benefits and Challenges of Multithreading:
#### Benefits:
- Improved performance by task parallelism.
- Responsiveness
- Resource sharing.

#### Challenges:
- Concurrency issue like deadlock,data inconsistency etc
- Synchornized overhead
- Testing and debuggin is difficult.


### Multitasking vs Multithreading

- process1 and process2 in the above picture thats called multitaksing
- Inside particular process/task we can create multiple threads is called multithreading
- In multitasking never share any resources.Whereas multithreading shares the resoruces in the task.


