# Lecture 1
## Goals
- Understand design and implementation of OS·
- Hands-on experience with xv6

## OS Purpose
- Abstract hardware
- **Multiplexing** hardware among apps -- run at the same time without interfere
- **Isolation** -- different app does not interfere unintentionally
- **Sharing** -- different app would like to interfere, interact, cooperate
- **Security**, permission / access control system 
- **Performance** -- full performance, the service does not get in the way
- Range of uses

## Organization

| Components  |         |                   |                | Space                 |
| ----------- | ------- | ----------------- | -------------- | --------------------- |
| VI          | CC      | Shell             | (Apps)         | User Space            |
|             |         |                   |                | Interface (focus)     |
| File System | Process | Memory Allocation | Access Control | Kernel Space  (focus) |
| CPU         | RAM     | Disk              | NET            | Hardware              |

## API-kernel, syscall
```
fd = open("out", 1); // file descriptor
write(fd, "hello\n", 6);

// create a new process identical to the caller, return process identifier
pid = fork(); 

```

## WHY OS course challenging and interesting?
- Environment is unforgiving
- Hardware simulator ``qemu``
- Tensions 
	- Both efficient and abstract
	- Powerful but simple API
	- Flexibility but security constraints
- Syscalls interact as well -- like open and fork, the new process has fd
- For SDE, eventually meant to know OS

## Linux System Call

```c
// copy.c
while (1){
	int n = read(0, buf, sizeof(buf));
	if (n <= 0){
		break;
	}
	write(1, buf, n);
}
exit(0);
// open.c  fork.c  exec.c  forkexec.c
int pid, status;

pid=fork();
if (pid == 0){
	char *argv[] = {"echo", "THIS", "IS", "ECHO", 0};
	exec("echo", argv);
	printf("exec failed!\n");
	exit(1);
}
else{
	printf("parent waiting\n");
	wait(&status);
	printf("the child exited with status %d.\n", status);
}
// redirect.c
close(1);
open("output.txt", O_WRONLY|O_CREATE);
char *argv[] = {"echo", "THIS", "IS", "redirected", "ECHO", 0};
exec("echo", argv);

```

``exec`` **replace** the current program with another program specified by the arguments, does not return unless error (error before it can layover).

copyOnWriteFork -- to eliminate apparent inefficiency of fork copying only to have exec, throw away the copy. Virtual Memory System tricks involved.  

Interleaving printing.

``wait(&status)`` take int pointer to monitor exit status, the return value is child 's pid.  

shell and fork: always fork and command is exec in child, parent still able to interact after child finishes.

redirection: child ``close(1)`` and newly open file would be guaranteed to be assigned with fd = 1. The interval between fork and exec in child gives shell to change fd, do redirection, etc.  

## Lab 1

Remember to close the fd whenever you don't need them.
``xargs`` may do some word splitting, and provide more customization with flags. In this lab we treat the whole line as a single argument.

# Lecture 3

## OS Organization topic
- isolation
- kernel/user mode
- system call
- xv6

util lab make use of syscall, interface between apps and OS. UNIX syscall interface.  

## Isolation
Bug in one app doesn't affect other app.  
Bug in app does not affect OS.

Strawman design -- if no OS, or OS just as a library ->  directly interact with hardware -> (not able to perform **enforced multiplexing**)  app give up for cooperative  scheduling -> infinite loop keeps using CPU; Also the memory would be dirtily written -> no **enforced strong memory isolation**.  OS as a lib may be seen in some RTOS.

## Unix interface is an abstraction
abstract the hardware resources 
processes: instead of CPU
exec: instead of memory
files: instead of disk blocks

## OS should be defensive
app cannot crash operating system
app cannot break out its own isolation
apps are perhaps malicious
Strong isolation between the apps and the OS -- typical by Hardware Support: **user/kernel mode; virtual memory**

## User/ Kernel mode
kernel mode, CPU can execute privileged instructions, manipulation on hardware, setting up protection like page tables, disabling clock interrupts, state of processor.

user mode, only unprivileged instructions, add sub, jr, branch.  

RISC-V also has machine mode, (supervisor mode = kernel mode)

question: why ecall is privileged? Exception mechanism and trap handler.

## CPU provides virtual memory
page table : virtual address -> physical
process has its own page table
OS itself has a page table
OS can set memory in a way every process has disjoint physical address, 
memory isolation -- view of memory per process

## Entering kernel
ecall \<n\>, system call number
Kernel should be trusted computing base
	- As few bug as possible
	- Treat user apps as malicious

## Components of Kernel
What should run in kernel mode?

monolithic kernel design -- linux, all os services (like vm, proc, fs) run on kernel mode, a lot of codes lead to bugs, but tight integration lead to good performance

micro kernel design -- very few components, (IPC, VM, multiplexing codes in kernel mode, fs, sh  in user space) , small kernel means fewer bugs, performance impaired

## xv6 kernel organization
- kernel, user, mkfs for file system
- .c -> .s -> .o and loader takes all .o files, as well as a ``kernel.asm`` file to make the kernel.
- what qemu is doing is just in an infinite loop, read, decode and execute instructions

## xv6 example
gdb 
- c/r -- continue/run till next breakpoint or end
- b -- break point
- s -- single step into the function
- n -- single step without descending into the function
- si -- step to next instruction
- finish -- finish the current function, loop, etc
- p(/x) \<name\> -- print variable (in hex format)
- p \<name\> @ \<n\>  -- print n values starting at name
- dis/en \<bnum\> -- disable/enable break point
- d \<bnum\> -- delete break point, if not specified, delete all break points
- layout split
- info b
- tui enable
- layout/focus asm
- layout/focus reg
- apropos * -- like help
- watch i
- i locals
- b breakpoint if var == val
initcode - hard coded, and modify ``p->trapframe->epc, p->trapfram->sp`` to turn to user mode

## Lab 2 Syscall:

**Trace**: printf directly output to console without going through write system call, so no worry about infinite loop.

To add a system call: 
- ``kernel/syscall.h`` defines the id of each syscall.
- ``kernel/syscall.c`` has a ``syscall()`` function where the system call number is read from the a7 of ``trapframe``. 
- ``kernel/sysproc.c`` or ``kernel/sysfile.c`` has ``sys_xxx()`` like functions, basically do the mapping then call static system calls. 
- ``user/user.h`` defines the API for user to trigger system call
- ``user/usys.pl`` would generate ``usys.S``, the actual system call stubs calling ``ecall``
- ``kernel/defs.h`` contains non-static kernel utils functions across kernel codes, 

static vs non-static functions: limited to the module, avoid name collision, allow optimization

# Lecture 4 Page Tables

## Address Spaces

Give every app including the kernel its own address space. So that ``cat`` wont overwrite ``sh``.

Question: could va be greater than pa? Yes. Think about `kalloc.c` and the free list becomes null, the OS would report error or do page swap.

Pagetables -- hardware support

cpu ---VA---> MMU ---PA--> Memory
sd $7,(a0)                   

From CPU perspective, it always issues, every instruction it issues, once MMU is enables, are virtual addresses.  To translate VA to PA, MMU maintains a table.  

The mapping itself is stored in memory, CPU has some register that points to, that contains the physical address of where the page table is stored.  So MMU only does mapping, according to a table in the physical memory, whose location is given by the CPU/instruction.

**Every app has its own map**. 

When CPU switch from one app to another app, it also switch the content of satp register pointing the root of the map for the appropriate process. 

Reading writing satp register is a privileged instruction.  

2^64 address, too painful to manage by an address table -- play by page 4KB in riscv. 

## Page Table 

in riscv, virtual address has limit 39 bit, 512 GB; physical address has limit 56 bit. Physical page is indexed by 44 bit number.

Virtual address, in total 2^39 VA approximately 512 GB
--  top 25 EXT | middle 27 index | tail 12 offset -- 

Use the middle 27 to look up into page table whose entity (PTE -- page table entry)
-- EXP |  44 Physical Page Number | 10 flags --

Then the PA looks like, maximum 2^56 , comes from the designer
-- 44 PPN | 12 Offset  --

## Multi Level Page Table

But 2^27 page table is still too big. We use three-level page table.

A block is 4096 byte, one entry (44 PPN (physical page number) + 10 Flag) takes 64 bits = 8 byte, ==> there would be 512 entries  in a single page, making it a page table.

The first 9 bits of 27 index is used to indexing the first level page table, get the physical address of corresponding second-level page table.

The middle 9 bits of 27 index is used to indexing the second level page table, locating the PA of third level page table.

The last 9 for final PA.

All the PPN are **physical address**, or we would stuck into a recursive translation.

The **satp** also stores **physical address**.  

Physical page and page table are **aligned**, i.e. each table takes the space of one page, therefore PPN concatenated with 0x000 would be the start of a physical page.  

## Translation Look-aside Buffer

Three memory reads for any virtual address reference is expensive, introducing TLB.

In riscv, it's like **cache** of pte entries \[VA, PA\] (but not addr, data). It can have different implementation, and it's hidden from OS, but inside Processor.

Once page table is switched, you need to flush TLB -- ``sfence_vma()``

All this table translation happens in hardware, xv6 models it using functions like ``walk()``; but not in operating system.  

There are also address - content caches, cache before MMU use virtual address, cache after MMU use physical address.  

## Indirection

Page tables provide level of indirection, making OS get full control of VA-PA translation, handling page fault. Providing OS tremendous amount of flexibility.  

## Memory organization in xv6

Physical memory organization is determined by the board.  (DRAM over 0x8000)

Below 0x800... are IO devices, interrupt related blocks, boot ROM, UART, VIRTIO disk and so on.

*Guard page* is not mapped to any physical page. *Trampoline and kstack* which lays high in virtual memory may be mapped to some lower physical memory which are already identically mapped. 

## w_satp

After this point, the whole world change to use page table, but with identity mapping, the next instruction would run normally (if not, may raise page fault etc)

## Lab 3 pgtbl

Remember the PTE_U flag when setting the address in user page table. 

Buffer should be initialized to 0 using ``char buf[64] = {0}``

Use walk() to get pointer to pte.


# Lecture 5 Assembly, Register, Stack

## RISC-V register

ABI: application binary interface. 
normal instruction is 64-bit long, while in some **compressed** version, it's only 16 bit. That's why some registers like ``s1`` are listed alone. ABI name and register name.

## Caller saved and callee saved

- Caller saved -- may not preserved across function call, like ``a0`` may be overwritten by invoking child function call. ``ra`` should be able to be overwritten because the callee may not return to the caller

- Callee saved -- should be preserved across function call, the function being called should worry about preserving the values in those registers. Like ``sp`` stack pointer and `s0/fp, s1` saved registers.

## Stack

Layout of stack frame

Return Address (to which line of code)
To previous frame (fp)
Saved register
local variables

Start from high address to low address. 

sp -- bottom of stack
fp -- top of current frame, then return addresses are always from a fix offset from fp.  

ra is caller saved, therefore have to maintain in a stack frame.  

## GDB

See lecture 3.

## Struct

fields are aligned


# Lecture 6 Isolation and System Call Entry_Exit



ecall does 3 things
change to supervisor mode
store pc to sepc
change pc to the value in stvec, address of trampoline frame

sret does 3 things
write pc with values in sepc
change to user mode
enable interupt

Then we need to store user regsiter, switch page table, create a stack, jump to kernel code.  

control a c -- qemu monitor, info mem- print page table

why wouldn't crash between ecall and pagetable switch? trampoline page is at the same address of virtual memory in kernel and processes and mapped to the same physical address!

trampoline.S, trap.c

# Lecture 7 pgtbl lab

## Per-process kernel page table
either copying or sharing -- anything above PLIC address are not going to be modified. Only assign the first level 0 page.

## RISCV kernel 
For debugging reason, when in supervisor mode, the CPU cannot manipulate ptes whose PTE_U flag is set to one. Therefore when we modifying per-process kernel page table, we should have that bit disabled.


# Lecture 8 Page Faults

## Information of faults
- Faulting va -- stval register
- type of page fault -- scause register 15 for write, 13 for read, 12 for instruction page fault, 8 for ecall
- faulting instructions -- sepc. After dealing, go back and re-execute the same instruction.
## Lazy allocate
- eager allocation
- In sbrk(), p->sz only increase size
- In usertrap, add condition for scause == 15
- When a write page fault occurs of 15, check feasibility, allocate memory and map. 
- In uvmunmap, when facing PTE_V flag problem, does not panic, just continue

## Zero filled on demand
There are many null pages, like BSS above data and text.  
User over ask for memory.
Map many va's to the same read-only null page
Once trying to store, get a page fault, and deal with it -- copy, update, restart instruction
Why? Save memory, save time for exec

## COW fork
Simple fork: Child address maps to physical address of parent, grant read-only access
Once child write page fault
	- copy page
	- map it, original be rw for parent, new be rw for child
	- restart instruction
How to distinguish cow or originally read-only?
	RSW pte flags
Be careful when freeing pages
	- There may be multiple page table referencing the same physical memory page, except the trampoline page (which would never be freed), 
	- Use ref count
Also need to add new process metadata field to maintain

## Demand Paging
Consider exec, once called, load text and data segment into memory in the case of eager allocation, takes a lot of time.
With page fault, 
	read block/page from file on demand into memory
	map memory into page table
	restart instruction

If out of memory, 
- evict a page to the file system.  -- LRU,   
- use the just-freed page.
- restart instruction

dirty page -- just written, may be written again; non-dirty page, just read from. 
Dirty bit - one position 7.
The latter is preferred. 

Clock algorithm -- set those access bits for LRU every time period.

## Memory mapped files
mmap(va, len, prot, flags, fd, offset)   copy from file
unmap(va, len) write back dirty block
no synchronization at this level

# Lecture 9 Interrupts

top
Athena as an example to show the amount of resident memory (physical) vs allocated virtual memory.

There are cases when hardware wants attention now. Software has to save its work, process the interrupt and restore its work. The saving and restoring really has mechanism similar to syscall or traps.

Three basic interrupt properties:
- Asynchronuous -- interrupt has nothing to do with the current running process on CPU. NOT AN EXAMPLE -- syscall  
- Concurrency -- CPU and interrupt device are operating in parallel
- Programmed -- external devices
## Drivers manage devices
bottom part -- interrupt handler, run in any context
top part -- user processes or rest of the kernel call, read and write calls into

there may be a queue used to decouple the top and the bottom from each other, allowing device run parallelly with CPU.  

## Programming device
memory mapped i/o, program use ordinary ld/st instruction to those address, read and write **control registers** of the device. Has to look into documentation of devices.  

## RISCV Interrupts Register
SIE -- supervisor interrupt enable, Software, External, Timer Interrupt.

SSTAUTS -- supervisor stats. There's one bit to open or close interrupt.  Every core has its own SIE and SSTATUS register.  

SIP -- supervisor interrupt pending, which kind of interruption

SCAUSE -- like trap, reason for entering kernel mode

STVEC -- like trap, program counter for resume

## Code

consoleinit() -> uartinit()  configure uart chip

plicinit() setting what kind of intr we would accept

plicinithart() for multicores interrupts initial configuration


## Interrupts and Concurrency
- CPU and device runs in parallel
- Interrupt stops the current program, can be avoided by disable interrupt bit
- top and bottom program can be run in parallec on different core -- need lock to synchronize

## Producer - Consumer Parallelism
A cyclic queue, read pointer and write pointer.
This data structure lives in RAM and is shared among cores -- why we need locks.

## Interrupt and Polling
Interrupt used to be fast and simple, now slow, device now is complicated.  

Polling:  CPU spins until device has data. Waste CPU cycles if device is slow. Save entry/exit cost if device is fast. 

May be combined with interrupt by switching between them.  

## Time Interrupt
timervec -- machine mode time trap handler
it schedules the next timer interrupt, and raise a supervisor software interrupt (and kernel would see a time interrupt happened)

# Lecture 10 Multiprocessors and Locks

race conditions
critical section

## Problems
- Dead locks
- Module layer
- Performance -- Split data structure, have to split locks

## Practice
- Start with coarse-grained lock
- Measure speedup -- find potential contented

## UART Lock

**Instruction**
amoswap addr, t1, t2 -- \_\_sync_lock_test_and_set(&lk->locked, 1), set (addr) to t1, set t2 to original data (addr) held.

st is not atomic, therefore we also need atomic instruction when release.

**Interrupt**
disable interrupt -- if able, in uartputc acquire(), we lock, but during this procedure, uart finish transmission of previour char, calling uartintr() would acquire the lock another time -- deadlock panic

spinlock needs to deal with concurrent between different CPU; same CPU but normal process and interrupt. For latter case, we need to disable interrupt until we release the lock.

**Ordering**
For ordering there should be **synchronize** instruction/ **memory fence**, telling ld/st instructions before it cannot be re-order after it.

# Lecture 11 Switching Threads

Thread : a serial of instructions.

xv6: User thread and kernel thread, only switch between kernel threads.  

User thread -- trapframe, kernel thread -- context(basically register and pc, kstack)

yield -- sched -- swtch - scheduler thread --....-- yield(continued)
Be careful about the lock! acquire always precedes swtch


# Lecture 13 Sleep and Wakeup

## Review switching threads
(In xv6) A process is not allowed to hold any lock other than p->lock when calling swtch.
Imagine p1 holding some lock be switched to p2, who's acquiring the same lock, in aquire() function, interrupt is disabled. deadlock.

## Coordination -- Sleep/Wake
- Use case pipes, disk read, wait(), when we need to wait for something.
- One implementation -- busy wait, loop, good if you know the task would be finished in a short time.

## Broken sleep
Take uartwrite and uartintr as an example, done is shared, need to be protected.

- First trial, use lock to protect critical section in two functions -- deadlock -- you cannot hold the lock when sleeping
- Second trial, release lock before we call sleep -- **lost wakeup** -- wake up before sleep
- The not-broken sleep is going to **atomically** put the process to sleep and release the lock

In sleep. First acquire process lock then release uart lock -- we cannot afford wake-up after release

In wakeup, called with condition lock, but won't look at the process until we release the proc lock in sleep. Guaranteed to hold both locks when we try to wake up.

Another example -- pipe read pipe write -- almost all sleep must be enclosed in a while loop.

## exit()
turn to zombie itself, then turn to unused and freed by parent. Cannot be done by a thread itself -- think about stack, pagetables etc.


# Lecture 14 File System

## Features
- User-friendly names / path names 
- Share files between users and processes
- Persistence and durability -- not like processes or other resources

## Why interesting
- Abstraction is useful
- Crash safety -- important and involved
- Disk layout
- Performance -- storage devices are typically slow, through buffer cache, concurrency(when one doing look up, other can do some other things)

## API example for file system calls
- ``fd = open("x/y", flags); write(fd, "abc", 3);`` 
- Notice that -- path name be human readable, offset is implicit, os must store it.
- ``link("x/y", "x/z");`` multiple to one relation
- ``unlink("x/y")`` -- reference count, 

- There are different ways of implementation.

## File System Structure

inode <- fileinfo, independent of name
- inode number
- link count
- open fd count
- can be deleted only both couts are zero
- fd must maintain offset

## File System Layers

Bottom to top

Disk -- dev

Buffer/Block Cache -- memory

logging

icache/inode cache for synchronization
pack into a single disk block

inode -- read write bytes

names, fds

## Storage Devices
SSD              vs     HDD
100us-1ms           10 ms

unit: 
sector 512 bytes; block (defined by file system) 1024 bytes on xv6

Sit on some bus connected to CPU, PCI PCIe protocol

## Disk Layout
Series of blocks
0 for boot or not used
1 for super blocks
2 - 32 log on xv6
32 - 45 inodes 64-byte (linear indexing)
45 - 46 bitmap block
after: data blocks, content of file/dirs

 meta data block -- 2 - 46

## On-Disk inode
type -- file or dir
nlink
size
12 direct block numbers
1 indirect block numbers -- a block itself holds 256 block numbers
To extend, could probably have double indirect blocks

## Directories
Basically a file with some structure -- unix

Consists of directory entries, each of which contains
| inode number | filename |

enough information for pathname lookup.  

root inode has fixed inode number

## Block Cache Properties
Only one copy of block in mem
Sleeplocks
LRU
Two levels of locking (bcache and block)  









# Lecture 21 Networking

### Overall Network Architecture

![[Excalidraw/OS-lec21-networking.md#^group=Ki05xYg3j7-HFhuFuJCga]]
![[Excalidraw/OS-lec21-networking.md#^group=G1uLT1UCYdg5GAuEPut7N]]

### Ethernet packet format

![[Excalidraw/OS-lec21-networking.md#^group=8fMxMJo4rxz4u7dkaRmDz]]

``` c
// Ethernet frame header
#define ETHADDR_LEN 6
struct eth{
	uint8 dhost[ETHADDR_LEN];
	uint8 shost[ETHADDR_LEN];
	uint16 type;
} __attribute__((packed));

#define ETHTYPE_IP 0x0800 // Internet protocol
#define ETHTYPE_ARP 0x0806 // Address resolution protocol
```

```
tcpdump output:
15:27:40 ARP, Request who-has 10.0.2.15 tell 10.0.2.2, length 28
0x0000: ffff ffff ffff 5255 0100 0202 0806 0001
0x0010: 0800 0604 0001 5255 0100 0202 0a00 0202
0x0020: 0000 0000 0000 0a00 020f
```

``tcpdump`` output of an ARP request: the first ``ffff ffff ffff`` stands for broadcast, the next ``5255 0a00 0202`` stands for source MAC address for NIC, the ``0806`` implies it's an ARP content and the rest is the payload.

NIC address -- first 24 bits for manufacturer and the rest for unique id.

Before the Ethernet frame there are 7 bytes **preamble**, then 1 byte **Start frame delimiter**. After the frame is 4 bytes (Cyclic Redundancy Check)  **Frame check sequence**. They  (usually hidden by the hardware) encapsule the Ethernet frame to become an **Ethernet packet** and between packets, the **IPG** (aka interpacket gap) is at least 12 bytes.

### Address Resolution Protocol

IP address has internal structure: high bits are full of hints about where the packets need to go.

```C
struct arp{
	uint16 hrd; // format of hardware address
	uint16 pro; // format of protocol address
	uint8 hln; // length of hardware address
	uint8 pln; // length of protocol address
	uint16 op; // operation

	char sha[ETHADDR_LEN];  // sender hardware address
	uint32 sip;  // sender ip address
	char tha[ETHADDR_LEN]; // target hardware address
	uint32 tip; // target ip address
}__attribute__((packed));

#define ARP_HRD_ETHER 1 // ethernet
enum {
	ARP_OP_REQUEST = 1, // requests hw addr given protocol addr
	ARP_OP_REPLY = 2, // replies a hw addr given protocol addr
};
```

```
tcpdump output:
15:27:40 ARP, Request who-has 10.0.2.15 tell 10.0.2.2, length 28
0x0000: ffff ffff ffff 5255 0100 0202 0806 0001
0x0010: 0800 0604 0001 5255 0100 0202 0a00 0202
0x0020: 0000 0000 0000 0a00 020f
```
``tcpdump`` output of an ARP request: after the ethernet frame header, we have ``0001`` as Ethernet hardware format for hw addr; ``0800`` as IP format for protocol addr; ``0604`` stands for the length -- 48 bit MAC addr and 32 bit IP addr; ``0001`` means ARP request operations; then followed source MAC, source IP, target MAC(in request, unknown), target IP.  

### IP packet header

Ether type would be 0800. 

During transmission, the ETH header may vary because of entering different network, but IP header and its content would mostly stay untouched from source host to destination host.

The most critical field: source, destination and (upper layer) protocol. Typical length 20 bytes.

```C
struct ip {
	uint8 ip_vhl; // version << 4 | header length >> 2, ie 32-bit word
	uint8 ip_tos;  // type of service
	uint16 ip_len; // total length of entire packet
	uint16 ip_id; // identification
	uint16 ip_off; // fragment offset field
	uint8 ip_ttl; // time to live
	uint8 ip_p; // protocol
	uint16 ip_sum; //checksum
	uint32 ip_src, ip_dst;
};

#define IPPROTO_TCP 6 // tcp -- transmission control protocol
#define IPPROTO_UDP 17 // udp -- user diagram protocol

```

```
tcpdum output:

time IP 10.0.2.15.2000 > 10.0.2.2.25603: UDP, length 19
0x0000 ffff ffff ffff 5254 0012 3456 0800 4500
0x0010 002f 0000 0000 6411 3eae 0a00 020f 0a00
0x0020 0202 07d0 6403 001b 0000 6120 6d65 7373  ....d.....a.mess
0x0030 6167 6520 6672 6f6d 2078 7636 21         age.from.xv6!

```

Analysis: ``4500`` 4 means ipv4 header,  5 (IHL) means header length 5x4 = 20 bytes; ``002f`` the entire IP packet is 47 byte long;  ``6411``: ``64`` is the ttl and ``11`` is the hex of integer 17, meaning the payload is in UDP protocol.  (19 of UDP header + 8 of UDP payload + 20 of IP header = 47)


### UDP packet header

 Once a packet is at the right host, what app should it go to?  UDP header sits inside IP packet
  contains src and dst port numbers  
  
  An application uses the "socket API" system calls to tell the kernel it would like to receive all packets sent to a particular port. Some ports are "well known", e.g. port 53 is reserved for DNS server, others are allocated as-needed for the client ends of connections. After the UDP header: payload, e.g. DNS request or response

```C
struct udp{
	uint16 sport; // source port
	uint16 dport; // destination port
	uint16 ulen;  // length including udp header, not inlcuding IP header
	uint16 sum;   // checksum
}
```

Analysis: ``1b`` means UDP packet is 27 bytes long.  xv6 doesn't fill the checksum field.

TCP: like UDP but has sequence number fields so it can retransmit lost packets, and match send rate to network and destination capacity.

Why limited size of packet length? Error correcting checksum may fail, buffer size, 

### Layering and queues, DMA rings



### Interrupts, livelock and polling









 




