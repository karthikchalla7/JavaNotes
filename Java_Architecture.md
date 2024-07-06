## JavaArchitecture

**How Java Code Excetues?**

- .java file  ---compile(entire file)--> .class file  -----interpreter(line by line)--> Machine code(0s and 1s).
- .java file is human readable file - this is the source code
- Then .java file is compiled to get the byte code with(.class file)
- Then interpret the byte code line by line into machine code.

**What is platform independence?**
- It means that byte code can run on all OS(linux,macos,etc)
- we need to convert source code to machine code so computer can understand
- Compiler helps in doing this by turing into executable code(like .exe file)
- This executable code is a set of instructions for the computer
- After compiling c/c++ code we get .exe file whihc is platform dependent
- Java is platform-independent but JVM is platform dependent.
- JVM converst byte code to machine code based on which type of OS
- Hence JVM is platform dependent.

**Architecture of Java**  
- Outermost to Innermost in the below order.  
- JDK = JRE + Development tools(outermost)
- JRE = JVM + library classes
- JVM ( java virtual machine)  
- JIT( just in time ) (innermost)

![image](https://github.com/karthikchalla7/JavaArchitecture/assets/76682351/ed6c7327-4c60-4058-8bd2-55c5eeb28fed)

**JDK** 
- Provides environment to develop and run the java program
- It is package that includes:
  1. Development tools - to provide an environemnt to develop your program
  2. JRE - to execute your program
  3. a compiler - javac
  4. archiever - jar
  5. docs generator - javadoc
  6. Interpreter/loader

**JRE**
- This is inside JDK
- It is an installation package that provides environment to ONLY RUN the program
- It consists of :
  1. Deployment technologies
  2. User interface toolkits
  3. Integration libraries
  4. Base libraries loading
  5. JVM
- After we get the .class file the next things happen at runtime
  1. class loader loads all classes needed to execute the program
  2. JVM sends byte code to byte code verifier to check the format of code.


**How JVM Works?(class Loader)**
- JVM contains the stack and heap memory allocations.
- Loading
  1. Read .class file and generate binary data
  2. an object of this class is created in heap memory
- Linking
  1. JVM verifies the .class file
  2. allocates memory for class variables and default variables
  3. replace symbolic references from the type with direct references(ex : int a = 10 while allocating memory instead of using a we use 10)
- Initialization:
  All static variables are assigned with their values defined in the code and static block

**JVM Exectuion**
- Interpreter:
  1. Line by line execution
  2. when one method is called many times it will interpret again and again
  3. This is time consuming as well as memory running again and again
-JIT (just in time)
 1. This is where JIT which is inside JVM will come into picture
 2. Those methods that are repeated JIT provides direct machine code so re-interpretation is not required
 3. makes execution faster and garbage collection done.

* JVM Does all the work for converting byte code to executable by using libraries from the JRE

**Compile Time**
- .java file ---- javac(compilation)---> .class file

**Runtime**
class loader---->byte code verifier----->interpreter----->runtime---->hardware

Java Source Code  
|  
JDK -----> Byte code ------> JVM ----->JRE
