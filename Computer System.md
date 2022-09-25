A Computer System consists of hardware and systems software that work together to run application programs.

``` --hello.c
#include <stdio.h> 
int main()
{
    printf("hello, world\n");
    return 0;
}
```

## 1.1 Information is Bits + Context

![image](https://user-images.githubusercontent.com/59940078/192087453-f31a4ece-c3b6-422c-a999-bdf41c962ec6.png)

The _hello_ program begins life as a source program (or source file) that the programmer creates with an editor and saves it as a text file called **hello.c**. The source program is a sequence of bits, each with a value of 0 or 1, organized in 8-bit chunks called bytes. Each byte represents some text character in the program.

Most computer systems represent text characters using the ASCII standard that represents each character with a unique byte-size integer value. The image above shows the ASCII representation of the **hello.c** program.

The **hello.c** program is stored in a file as sequence of bytes. Each byte has an interger value that corresponds to some character. For example, "#" represents the integer value of 35, "i" represents the integer value 105 and so on. Each text line is terminated by the invisible newline character "\n", which is represented by the integer value of 10. Files such as **hello.c** that consist exclusively of ASCII characters are known as text files. All other files are known as binary files.

The representation of **hello.c** illustrates a _fundamental idea_: All information in a system - including disk files, programs stored in memory, user data stored in memory, and data transferred across a network - is represented as a bunch of bits. The only thing that distinguishes different data objects is the context in whcih we view them. For example, in different contexts, the same sequence of bytes might represent an integerm floating-point number, character string or machine instruction.

## 1.2 Programs are translated by other programs into different forms

The **hello** program begins life as a high-level C program because it can be read and understood by human beings in that form. However, in order to run **hello.c** on the system, the individual C statements must be translated by other programs into a sequence of _low-level machine-language_ instructions. These instructions are then packaged in a form called an _executable object program_ and stored as a binary disk file. Object programs are also referred to as executable object files.

On a UNIX system, the translation from the source file is performed by a compiler driver.

<img width="725" alt="image" src="https://user-images.githubusercontent.com/59940078/192132109-eb84aec2-fbfb-4439-a5fc-815f22304031.png">

linux> gcc -o hello hello.c

Here, the gcc compiler driver reads the source file **hello.c** and translates it into an executable file _hello_. The translation is performed in the sequence of four phases in Figure 1.3. The programs that perform the four phases (preprocessor, compiler, assembler, and linker) are known collectively as the _compilation system_.

- **_Preprocessing phase_**: The preprocessor (cpp) modifies the original C program according to directives that begin with the ‘#’ character. For example, the #include <stdio.h> command in line 1 of hello.c tells the preprocessor to read the contents of the system header file stdio.h and insert it directly into the program text. The result is another C program, typically with the .i suffix.

- **_Compilation phase_**: The compiler (cc1) translates the text file **hello.i** into the text file **hello.s**, which contains an _assembly-language program_. This program includes the following definition of function main. Each of lines 2–7 in the image below in this definition describes one low-level machine- language instruction in a textual form. Assembly language is useful because it provides a common output language for different compilers for different high-level languages. For example, C compilers and Fortran compilers both generate output files in the same assembly language.

![image](https://user-images.githubusercontent.com/59940078/192147536-747e5f74-211b-4f48-947b-0572c3d3eb77.png)

- **_Assembly phase_**: Next, the assembler (as) translates hello.s into machine-language instructions, packages them in a form known as a _relocatable object program_, and stores the result in the object file hello.o. This file is a binary file containing 17 bytes to encode the instructions for function main. If we were to view hello.o with a text editor, it would appear to be gibberish.

- **_Linking phase_**: Notice that our hello program calls the _printf_ function, which is part of the _standard C library_ provided by every C compiler. The _printf_ function resides in a separate precompiled object file called _printf.o_, which must somehow be merged with our **hello.o** program. The linker (ld) handles this merging. The result is the **hello** file, which is an executable object file (or simply _executable_) that is ready to be loaded into memory and executed by the system.

## 1.3 It Pays to Understand How Compilation Systems Work

Modern compilers are sophisticated tools that usually produce good code. As a programmer, we generally do not need to know the inner workings of the compiler in order to write efficient code. However, in order to make good coding decisions, we do need a basic understanding of machine-level code.

- **_Optimizing program performance_**:  For example, is a switch statement always more efficent than a sequence of if-else statements? How much overhead is incurred by a function call? Is a _while_ loop more efficient than a _for_ loop? Are pointer references more efficient than array indexes? Why does our loop run so much faster if we sum into a local variable instead of an argument that is passed by reference? How can a function run faster when we simply rearrange the parentheses in an arithmetic expression?

- **_Understanding link-time errors_**: Some of the most perplexing programming errors are related to the operation of the linker, especially when you are trying to build large software systems. For example, what does it mean when the linker reports that it cannot resolve a reference? What is the difference between a static variable and a global variable? What happens if you define two global variables in different C files with the same name? What is the difference between a static library and a dynamic library? Why does it matter what order we list libraries on the command line? And scariest of all, why do some linker-related errors not appear until run time?

- **_Avoiding security holes_**: For many years, _buffer overflow vulnerabilities_ have accounted for many of the security holes in network and Internet servers. These vulnerabilities exist because too few programmers understand the need to carefully restrict the quantity and forms of data they accept from untrusted sources. A first step in learning secure programming is to understand the con- sequences of the way data and control information are stored on the program stack.

## 1.4 Processors Read and Interpret Instructions Stored in Memory
### 1.4.1 Hardware Organization of a System
To understand what happens to our hello program when we run it, we need to understand the hardware organization of a typical system, which is shown in Figure 1.4 (modeled after the family of recent Intel systems).

![image](https://user-images.githubusercontent.com/59940078/192150698-991c3118-0f53-4b9b-9eff-1279352f2971.png)

- **_Buses_**: Running throughout the system is a collection of electrical conduits called _buses_ that carry bytes of information back and forth between the components. Buses are typically designed to transfer fixed-size chunks of bytes known as _words_. The number of bytes in a word (the _word size_) is a fundamental system parameter that varies across systems. Most machines today have word sizes of either 4 bytes (32 bits) or 8 bytes (64 bits).

- **_IO Devices_**: Input/output (I/O) devices are the system’s connection to the external world. Our example system has four I/O devices: a keyboard and mouse for user input, a display for user output, and a disk drive (or simply disk) for long-term storage of data and programs. Initially, the executable hello program resides on the disk.

    Each I/O device is connected to the I/O bus by either a _controller_ or an _adapter_. The distinction between the two is mainly one of packaging. Controllers are chip sets in the device itself or on the system’s main printed circuit board (often called the _motherboard_). An adapter is a card that plugs into a slot on the motherboard. Regardless, the purpose of each is to transfer information back and forth between the I/O bus and an I/O device.
    
- **_Main Memory_**: The _main memory_ is a temporary storage device that holds both a program and the data it manipulates while the processor is executing the program. Physically, main memory consists of a collection of _dynamic random access memory_ (DRAM) chips. Logically, memory is organized as a linear array of bytes, each with its own unique address (array index) starting at zero. In general, each of the machine instructions that constitute a program can consist of a variable number of bytes. The sizes of data items that correspond to C program variables vary according to type. For example, on an x86-64 machine running Linux, data of type short require 2 bytes, types _int_ and _float_ 4 bytes, and types _long_ and _double_ 8 bytes.

- **_Processor_**: 
The _central processing unit_ (CPU), or simply processor, is the engine that inter- prets (or _executes_) instructions stored in main memory. At its core is a word-size storage device (or _register_) called the _program counter_ (PC). At any point in time, the PC points at (contains the address of) some machine-language instruction in main memory.

    From the time that power is applied to the system until the time that the power is shut off, a processor repeatedly executes the instruction pointed at by the program counter and updates the program counter to point to the next instruction. A processor _appears_ to operate according to a very simple instruction execution model, defined by its _instruction set architecture_. In this model, instructions execute in strict sequence, and executing a single instruction involves performing a series of steps. The processor reads the instruction from memory pointed at by the program counter (PC), interprets the bits in the instruction, performs some simple operation dictated by the instruction, and then updates the PC to point to the next instruction, which may or may not be contiguous in memory to the instruction that was just executed.
    
    There are only a few of these simple operations, and they revolve around main memory, the _register_ file, and the _arithmetic/logic unit_ (ALU). The register file is a small storage device that consists of a collection of word-size registers, each with its own unique name. The ALU computes new data and address values. Here are some examples of the simple operations that the CPU might carry out at the request of an instruction:
    
    - _Load_: Copy a byte or a word from main memory into a register, overwriting the previous contents of the register.
        
    - _Store_: Copy a byte or a word from a register to a location in main memory, overwriting the previous contents of that location.
        
    - _Operate_: Copy the contents of two registers to the ALU, perform an arithmetic operation on the two words, and store the result in a register, overwriting the previous contents of that register.
        
    - _Jump_: Extract a word from the instruction itself and copy that word into the program counter (PC), overwriting the previous value of the PC.
    
    A processor appears to be a simple implementation of its in- struction set architecture, but in fact modern processors use far more complex mechanisms to speed up program execution. Thus, we can distinguish the processor’s instruction set architecture, describing the effect of each machine-code instruction, from its _microarchitecture_, describing how the processor is actually implemented.

### 1.4.2 Running the _hello_ Program

Initially, the shell program is executing its instructions, waiting for us to type a command. As we type the characters **./hello** at the keyboard, the shell program reads each one into a register and then stores it in memory, as shown in Figure 1.5.

When we hit the enter key on the keyboard, the shell knows that we have finished typing the command. The shell then loads the executable _hello_ file by executing a sequence of instructions that copies the code and data in the hello object file from disk to main memory. The data includes the string of characters hello, world\n that will eventually be printed out.

![image](https://user-images.githubusercontent.com/59940078/192152034-96bf7eca-82a7-450f-9b1c-ed888a105a44.png)

Using a technique known as _direct memory access_ (DMA, discussed in Chapter 6), the data travel directly from disk to main memory, without passing through the processor. This step is shown in Figure 1.6.

Once the code and data in the hello object file are loaded into memory, the processor begins executing the machine-language instructions in the hello program’s main routine. These instructions copy the bytes in the _hello, world\n_ string from memory to the register file, and from there to the display device, where they are displayed on the screen. This step is shown in Figure 1.7.

## 1.5 Caches Matter

An important lesson from this simple example is that a system spends a lot of time moving information from one place to another. The machine instructions in the hello program are originally stored on disk. When the program is loaded, they are copied to main memory. As the processor runs the program, instruc- tions are copied from main memory into the processor. Similarly, the data string _hello,world\n_, originally on disk, is copied to main memory and then copied from main memory to the display device. From a programmer’s perspective, much of this copying is overhead that slows down the “real work” of the program. Thus, a major goal for system designers is to make these copy operations run as fast as possible.

Because of physical laws, larger storage devices are slower than smaller storage devices. And faster devices are more expensive to build than their slower counterparts. For example, the disk drive on a typical system might be 1,000 times larger than the main memory, but it might take the processor 10,000,000 times longer to read a word from disk than from memory.

![image](https://user-images.githubusercontent.com/59940078/192152321-9824ec27-8528-4f70-8f0d-035687910f72.png)

![image](https://user-images.githubusercontent.com/59940078/192152338-422c388d-bd76-46d3-a63f-f203e2bf5126.png)

Similarly, a typical register file stores only a few hundred bytes of information, as opposed to billions of bytes in the main memory. However, the processor can read data from the register file almost 100 times faster than from memory. Even more troublesome, as semiconductor technology progresses over the years, this _processor–memory gap_ continues to increase. It is easier and cheaper to make processors run faster than it is to make main memory run faster.

To deal with the processor–memory gap, system designers include smaller, faster storage devices called _cache memories_ (or simply caches) that serve as temporary staging areas for information that the processor is likely to need in the near future. Figure 1.8 shows the cache memories in a typical system. An _L1 cache_ on the processor chip holds tens of thousands of bytes and can be accessed nearly as fast as the register file. A larger _L2 cache_ with hundreds of thousands to millions of bytes is connected to the processor by a special bus. It might take 5 times longer for the processor to access the _L2 cache_ than the _L1 cache_, but this is still 5 to 10 times faster than accessing the main memory. The L1 and L2 caches are implemented with a hardware technology known as _static random access memory_ (SRAM). Newer and more powerful systems even have three levels of cache: L1, L2, and L3. The idea behind caching is that a system can get the effect of both a very large memory and a very fast one by exploiting _locality_, the tendency for programs to access data and code in localized regions. By setting up caches to hold data that are likely to be accessed often, we can perform most memory operations using the fast caches.

![image](https://user-images.githubusercontent.com/59940078/192153617-93552dd3-0954-46ae-9c44-fe6eddf33589.png)

## 1.6 Storage Devices Form a Hierarchy

This notion of inserting a smaller, faster storage device (e.g., cache memory) between the processor and a larger, slower device (e.g., main memory) turns out to be a general idea. In fact, the storage devices in every computer system are organized as a _memory hierarchy_ similar to Figure 1.9. As we move from the top of the hierarchy to the bottom, the devices become slower, larger, and less costly per byte. The register file occupies the top level in the hierarchy, which is known as level 0 or L0. We show three levels of caching L1 to L3, occupying memory hierarchy levels 1 to 3. Main memory occupies level 4, and so on.

The main idea of a memory hierarchy is that storage at one level serves as a cache for storage at the next lower level. Thus, the register file is a cache for the L1 cache. Caches L1 and L2 are caches for L2 and L3, respectively. The L3 cache is a cache for the main memory, which is a cache for the disk. On some networked systems with distributed file systems, the local disk serves as a cache for data stored on the disks of other systems.

## 1.7 The Operating System Manages the Hardware

When the shell loaded and ran the _hello_ program, and when the _hello_ program printed its message, neither program accessed the keyboard, display, disk, or main memory directly. Rather, they relied on the services provided by the _operating system_. We can think of the operating system as a layer of software interposed between the application program and the hardware, as shown in Figure 1.10. All attempts by an application program to manipulate the hardware must go through the operating system.

![image](https://user-images.githubusercontent.com/59940078/192156318-6714dd12-3720-4f7a-9000-fc44ea01bc6a.png)

The operating system has two primary purposes: (1) to protect the hardware from misuse by runaway applications and (2) to provide applications with simple and uniform mechanisms for manipulating complicated and often wildly different low-level hardware devices. The operating system achieves both goals via the fundamental abstractions shown in Figure 1.11: _processes_, _virtual memory_, and _files_. As this figure suggests, files are abstractions for I/O devices, virtual memory is an abstraction for both the main memory and disk I/O devices, and processes are abstractions for the processor, main memory, and I/O devices. We will discuss each in turn.

### 1.7.1 Processes

When a program such as hello runs on a modern system, the operating system provides the illusion that the program is the only one running on the system. The program appears to have exclusive use of both the processor, main memory, and I/O devices. The processor appears to execute the instructions in the program, one after the other, without interruption. And the code and data of the program appear to be the only objects in the system’s memory. These illusions are provided by the notion of a process, one of the most important and successful ideas in computer science.

A process is the operating system’s abstraction for a running program. Multi- ple processes can run concurrently on the same system, and each process appears to have exclusive use of the hardware. By concurrently, we mean that the instructions of one process are interleaved with the instructions of another process. In most systems, there are more processes to run than there are CPUs to run them.

Traditional systems could only execute one program at a time, while newer _multi-core_ processors can execute several programs simultaneously. In either case, a single CPU can appear to execute multiple processes concurrently by having the processor switch among them. The operating system performs this interleaving with a mechanism known as _context switching_. To simplify the rest of this discussion, we consider only a _uniprocessor_ system containing a single CPU.

The operating system keeps track of all the state information that the process needs in order to run. This state, which is known as the _context_, includes information such as the current values of the PC, the register file, and the contents of main memory. At any point in time, a uniprocessor system can only execute the code for a single process. When the operating system decides to transfer control from the current process to some new process, it performs a _context switch_ by saving the context of the current process, restoring the context of the new process, and then passing control to the new process. The new process picks up exactly where it left off. Figure 1.12 shows the basic idea for our example hello scenario.

![image](https://user-images.githubusercontent.com/59940078/192156503-e340bc50-5683-4e9b-9da6-1661b04ae0ef.png)

### 1.7.2 Threads

Although we normally think of a process as having a single control flow, in modern systems a process can actually consist of multiple execution units, called threads, each running in the context of the process and sharing the same code and global data. Threads are an increasingly important programming model because of the requirement for concurrency in network servers, because it is easier to share data between multiple threads than between multiple processes, and because threads are typically more efficient than processes. Multi-threading is also one way to make programs run faster when multiple processors are available, as we will discuss in Section 1.9.2.

![image](https://user-images.githubusercontent.com/59940078/192156614-97ba142e-8fb1-4d99-b403-b8a0f013d914.png)

### 1.7.3 Virtual Memory

_Virtual memory_ is an abstraction that provides each process with the illusion that it has exclusive use of the main memory. Each process has the same uniform view of memory, which is known as its _virtual address space_. The virtual address space for Linux processes is shown in Figure 1.13. (Other Unix systems use a similar layout.) In Linux, the topmost region of the address space is reserved for code and data in the operating system that is common to all processes. The lower region of the address space holds the code and data defined by the user’s process. Note that addresses in the figure increase from the bottom to the top.

The virtual address space seen by each process consists of a number of well- defined areas, each with a specific purpose. You will learn more about these areas later in the book, but it will be helpful to look briefly at each, starting with the lowest addresses and working our way up:

    - _Program code and data_.Code begins at the same fixed address for all processes, followed by data locations that correspond to global C variables. The code and data areas are initialized directly from the contents of an executable object file—in our case, the hello executable. 
    
    - _Heap_. The code and data areas are followed immediately by the run-time heap. Unlike the code and data areas, which are fixed in size once the process begins running, the heap expands and contracts dynamically at run time as a result of calls to C standard library routines such as malloc and free.
    
    - _Shared libraries_. Near the middle of the address space is an area that holds the code and data for shared libraries such as the C standard library and the math library. The notion of a shared library is a powerful but somewhat difficult concept.
    
    - _Stack_. At the top of the user’s virtual address space is the user stack that the compiler uses to implement function calls. Like the heap, the user stack expands and contracts dynamically during the execution of the program. In particular, each time we call a function, the stack grows. Each time we return from a function, it contracts.
    
    - _Kernel virtual memory_. The top region of the address space is reserved for the kernel. Application programs are not allowed to read or write the contents of this area or to directly call functions defined in the kernel code. Instead, they must invoke the kernel to perform these operations.
    
For virtual memory to work, a sophisticated interaction is required between the hardware and the operating system software, including a hardware translation of every address generated by the processor. The basic idea is to store the contents of a process’s virtual memory on disk and then use the main memory as a cache for the disk.
    
### 1.7.4 Files

A **file** is a sequence of bytes, nothing more and nothing less. Every I/O device, including disks, keyboards, displays, and even networks, is modeled as a file. All input and output in the system is performed by reading and writing files, using a small set of system calls known as **Unix I/O**.

This simple and elegant notion of a file is nonetheless very powerful because it provides applications with a uniform view of all the varied I/O devices that might be contained in the system. For example, application programmers who manipulate the contents of a disk file are blissfully unaware of the specific disk technology. Further, the same program will run on different systems that use different disk technologies. You will learn about Unix I/O in Chapter 10.

## 1.8 Systems Communicate with Other Systems Using Networks

Up to this point in our tour of systems, we have treated a system as an isolated collection of hardware and software. In practice, modern systems are often linked to other systems by networks. From the point of view of an individual system, the network can be viewed as just another I/O device, as shown in Figure 1.14. When the system copies a sequence of bytes from main memory to the network adapter, the data flow across the network to another machine, instead of, say, to a local disk drive. Similarly, the system can read data sent from other machines and copy these data to its main memory.

With the advent of global networks such as the Internet, copying information from one machine to another has become one of the most important uses of computer systems. For example, applications such as email, instant messaging, the World Wide Web, FTP, and telnet are all based on the ability to copy information over a network.

![image](https://user-images.githubusercontent.com/59940078/192156884-4b2ea8d9-10bf-4cc6-986e-fc1c4c8811d7.png)

Returning to our hello example, we could use the familiar telnet application to run _hello_ on a remote machine. Suppose we use a telnet _client_ running on our local machine to connect to a telnet _server_ on a remote machine. After we log in to the remote machine and run a shell, the remote shell is waiting to receive an input command. From this point, running the _hello_ program remotely involves the five basic steps shown in Figure 1.15.

After we type in the _hello_ string to the telnet client and hit the _enter_ key, the client sends the string to the telnet server. After the telnet server receives the string from the network, it passes it along to the remote shell program. Next, the remote shell runs the _hello_ program and passes the output line back to the telnet server. Finally, the telnet server forwards the output string across the network to the telnet client, which prints the output string on our local terminal.

This type of exchange between clients and servers is typical of all network applications.

## 1.9 Important Themes

An important idea to take away from this discussion is that a system is more than just hardware. It is a collection of intertwined hardware and systems software that must cooperate in order to achieve the ultimate goal of running application programs.

### 1.9.1 Amdahl's Law

Gene Amdahl, one of the early pioneers in computing, made a simple but insightful observation about the effectiveness of improving the performance of one part of a system. This observation has come to be known as _Amdahl’s law_. The main idea is that when we speed up one part of a system, the effect on the overall system performance depends on both how significant this part was and how much it sped up. Consider a system in which executing some application requires time Told. Suppose some part of the system requires a fraction α of this time, and that we improve its performance by a factor of k. That is, the component originally required time αTold, and it now requires time (αTold)/k. The overall execution time would thus be

![image](https://user-images.githubusercontent.com/59940078/192157084-a4e10242-0d05-48a2-8595-4ee939145846.png)

One interesting special case of Amdahl’s law is to consider the effect of setting k to ∞. That is, we are able to take some part of the system and speed it up to the point at which it takes a negligible amount of time. We then get

![image](https://user-images.githubusercontent.com/59940078/192157132-b3226066-f32a-4201-9a59-88c8a8e637b9.png)

So, for example, if we can speed up 60% of the system to the point where it requires close to no time, our net speedup will still only be 1/0.4 = 2.5×.
Amdahl’s law describes a general principle for improving any process. In addition to its application to speeding up computer systems, it can guide a company trying to reduce the cost of manufacturing razor blades, or a student trying to improve his or her grade point average. Perhaps it is most meaningful in the world of computers, where we routinely improve performance by factors of 2 or more. Such high factors can only be achieved by optimizing large parts of a system.

### 1.9.2 Concurrency and Parallelism


Throughout the history of digital computers, two demands have been constant forces in driving improvements: we want them to do more, and we want them to run faster. Both of these factors improve when the processor does more things at once. We use the term _concurrency_ to refer to the general concept of a system with multiple, simultaneous activities, and the term _parallelism_ to refer to the use of concurrency to make a system run faster. Parallelism can be exploited at multiple levels of abstraction in a computer system. We highlight three levels here, working from the highest to the lowest level in the system hierarchy.

- Thread-Level Concurrency: Building on the process abstraction, we are able to devise systems where multiple programs execute at the same time, leading to concurrency. With threads, we can even have multiple control flows executing within a single process. Support for concurrent execution has been found in computer systems since the advent of time-sharing in the early 1960s. Traditionally, this concurrent execution was only simulated, by having a single computer rapidly switch among its executing processes, much as a juggler keeps multiple balls flying through the air. This form of concurrency allows multiple users to interact with a system at the same time, such as when many people want to get pages from a single Web server. It also allows a single user to engage in multiple tasks concurrently, such as having a Web browser in one window, a word processor in another, and streaming music playing at the same time. Until recently, most actual computing was done by a single processor, even if that processor had to switch among multiple tasks. This configuration is known as a uniprocessor system.

    When we construct a system consisting of multiple processors all under the control of a single operating system kernel, we have a multiprocessor system. Such systems have been available for large-scale computing since the 1980s, but they have more recently become commonplace with the advent of multi-core processors and hyperthreading. Figure 1.16 shows a taxonomy of these different processor types.

    Multi-core processors have several CPUs (referred to as “cores”) integrated onto a single integrated-circuit chip. Figure 1.17 illustrates the organization of a typical multi-core processor, where the chip has four CPU cores, each with its own L1 and L2 caches, and with each L1 cache split into two parts—one to hold recently fetched instructions and one to hold data. The cores share higher levels of cache as well as the interface to main memory. Industry experts predict that they will be able to have dozens, and ultimately hundreds, of cores on a single chip.
    
    ![image](https://user-images.githubusercontent.com/59940078/192157267-d9f2edc2-c330-4aad-8f82-ad7765ec3d34.png)

    ![image](https://user-images.githubusercontent.com/59940078/192157279-3c4b3634-cb6b-4c77-9b64-55433991a3c9.png)

    Hyperthreading, sometimes called simultaneous multi-threading, is a tech- nique that allows a single CPU to execute multiple flows of control. It involves having multiple copies of some of the CPU hardware, such as program counters and register files, while having only single copies of other parts of the hardware, such as the units that perform floating-point arithmetic. Whereas a conventional processor requires around 20,000 clock cycles to shift between different threads, a hyperthreaded processor decides which of its threads to execute on a cycle-by- cycle basis. It enables the CPU to take better advantage of its processing resources. For example, if one thread must wait for some data to be loaded into a cache, the CPU can proceed with the execution of a different thread. As an example, the In- tel Core i7 processor can have each core executing two threads, and so a four-core system can actually execute eight threads in parallel.
    
    The use of multiprocessing can improve system performance in two ways. First, it reduces the need to simulate concurrency when performing multiple tasks. As mentioned, even a personal computer being used by a single person is expected to perform many activities concurrently. Second, it can run a single application program faster, but only if that program is expressed in terms of multiple threads that can effectively execute in parallel. Thus, although the principles of concur- rency have been formulated and studied for over 50 years, the advent of multi-core and hyperthreaded systems has greatly increased the desire to find ways to write application programs that can exploit the thread-level parallelism available with the hardware.
    
- Instruction-Level Parallelism: At a much lower level of abstraction, modern processors can execute multiple instructions at one time, a property known as instruction-level parallelism. For example, early microprocessors, such as the 1978-vintage Intel 8086, required multiple (typically 3–10) clock cycles to execute a single instruction. More recent processors can sustain execution rates of 2–4 instructions per clock cycle. Any given instruction requires much longer from start to finish, perhaps 20 cycles or more, but the processor uses a number of clever tricks to process as many as 100 instructions at a time. In Chapter 4, we will explore the use of pipelining, where the actions required to execute an instruction are partitioned into different steps and the processor hardware is organized as a series of stages, each performing one of these steps. The stages can operate in parallel, working on different parts of different instructions. We will see that a fairly simple hardware design can sustain an execution rate close to 1 instruction per clock cycle.

    Processors that can sustain execution rates faster than 1 instruction per cycle are known as superscalar processors. Most modern processors support superscalar operation. In Chapter 5, we will describe a high-level model of such processors. We will see that application programmers can use this model to understand the performance of their programs. They can then write programs such that the gen- erated code achieves higher degrees of instruction-level parallelism and therefore runs faster.
    
- Single-Instruction, Multiple-Data (SIMD) Parallelism: At the lowest level, many modern processors have special hardware that allows a single instruction to cause multiple operations to be performed in parallel, a mode known as single-instruction, multiple-data (SIMD) parallelism. For example, recent generations of Intel and AMD processors have instructions that can add 8 pairs of single-precision floating-point numbers (C data type float) in parallel.

    These SIMD instructions are provided mostly to speed up applications that process image, sound, and video data. Although some compilers attempt to auto- matically extract SIMD parallelism from C programs, a more reliable method is to write programs using special vector data types supported in compilers such as gcc.
    
### 1.9.3 The Importance of Abstractions in Computer Systems

The use of abstractions is one of the most important concepts in computer science. For example, one aspect of good programming practice is to formulate a simple application program interface (API) for a set of functions that allow programmers to use the code without having to delve into its inner workings. Different programming languages provide different forms and levels of support for abstraction, such as Java class declarations and C function prototypes.

![image](https://user-images.githubusercontent.com/59940078/192157404-0305665a-e133-4d32-aa9c-1e395a5a413c.png)

We have already been introduced to several of the abstractions seen in computer systems, as indicated in Figure 1.18. On the processor side, the instruction set architecture provides an abstraction of the actual processor hardware. With this abstraction, a machine-code program behaves as if it were executed on a processor that performs just one instruction at a time. The underlying hardware is far more elaborate, executing multiple instructions in parallel, but always in a way that is consistent with the simple, sequential model. By keeping the same execu- tion model, different processor implementations can execute the same machine code while offering a range of cost and performance.

On the operating system side, we have introduced three abstractions: files as an abstraction of I/O devices, virtual memory as an abstraction of program memory, and processes as an abstraction of a running program. To these abstractions we add a new one: the virtual machine, providing an abstraction of the entire computer, including the operating system, the processor, and the programs. The idea of a virtual machine was introduced by IBM in the 1960s, but it has become more prominent recently as a way to manage computers that must be able to run programs designed for multiple operating systems (such as Microsoft Windows, Mac OS X, and Linux) or different versions of the same operating system.

## 1.10 Summary

A computer system consists of hardware and systems software that cooperate to run application programs. Information inside the computer is represented as groups of bits that are interpreted in different ways, depending on the context. Programs are translated by other programs into different forms, beginning as ASCII text and then translated by compilers and linkers into binary executable files.

Processors read and interpret binary instructions that are stored in main memory. Since computers spend most of their time copying data between memory, I/O devices, and the CPU registers, the storage devices in a system are arranged in a hierarchy, with the CPU registers at the top, followed by multiple levels of hardware cache memories, DRAM main memory, and disk storage. Storage devices that are higher in the hierarchy are faster and more costly per bit than those lower in the hierarchy. Storage devices that are higher in the hierarchy serve as caches for devices that are lower in the hierarchy. Programmers can optimize the performance of their C programs by understanding and exploiting the memory hierarchy.

The operating system kernel serves as an intermediary between the applica- tion and the hardware. It provides three fundamental abstractions: (1) Files are abstractions for I/O devices. (2) Virtual memory is an abstraction for both main memory and disks. (3) Processes are abstractions for the processor, main memory, and I/O devices.

Finally, networks provide ways for computer systems to communicate with one another. From the viewpoint of a particular system, the network is just another I/O device.
