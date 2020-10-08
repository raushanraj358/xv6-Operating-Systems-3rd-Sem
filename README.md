# Scheduling policies in xv6

This patch of xv6 aims to present 4 different scheduling policies which can be used in xv6.
The 5 policies implemented: DEFAULT, FCFS, FRR, and MLQ. 
In order to enable a specific policy, when you launch qemu you have to specify the command above, which will set a flag that will enable the scheduling policity specified in it.

```
$ make qemu SCHEDFLAG=FCFS
```

If the flag isn't defined at launch, then DEFAULT (Round-Robin policy) is used.

A dummy file test.c is been created to see how differant process are scheduling.
Usage:
```
$ test &;
```

## Changes Made in File
* Changed scheduler() and allocproc() in proc.c
* Chnaged struct proc in proc.h --added priority and ctime.
* Added system call chpr(int pid,int priority) for changing priority (filed used change.c) --Usage given in MLQ
* Added system call getNumProc() for ps command (file used ps.c)  --Usage given below:
```
$ ps
```

## Polices

* [DEFAULT](#default---round-robin) - This is the default Round-Robin policy which comes with the vanilla version of xv6
* [FCFS](#fcfs---first-come-first-served) - **F**irst **C**ome **F**irst **S**erved Non-Preemptive
* [FRR](#frr---fifo-round-robin-scheduling-algoritm) - **FIFO** **R**ound **R**obin Scheduling
* [MLQ](#mlq---multilevel-queue-scheduling) - **M**ulti­**L**evel **Q**ueue scheduling

## DEFAULT - Round Robin

The default algorithm implemented in xv6 it's one of the simplest (with FCFS) and relies on the Round-Robin policy, basically it loops through all the process which are available to run (market with the ```RUNNABLE```) state and give access to
CPU at each one of them one at a time.
To schedule processes fairly, a round-robin scheduler generally employs time-sharing, giving each job a time slot or quantum (its allowance of CPU time), and interrupting the job if it is not completed by then. 
The job is resumed next time a time slot is assigned to that process. 
If the process terminates or changes its state to waiting during its attributed time quantum, the scheduler selects the first process in the ready queue to execute. 
In the absence of time-sharing, or if the quanta were large relative to the sizes of the jobs, a process that produced large jobs would be favoured over other processes.
Round-robin scheduling is simple, easy to implement, and starvation-free.

To enable it and see how DEFAULT works use this command when compiling xv6:

```
$ make qemu SCHEDFLAG=DEFAULT
```

## FCFS - First Come First Served

First come first served (FCFS), is the simplest scheduling algorithm. FCFS simply queues processes in the order that they arrive in the ready queue. 
The scheduling overhead due to using this policy is minimal since context switches only occur upon process termination, and no reorganization of the process queue is required.
Throughput can be low, because long processes can be holding CPU, waiting the short processes for a long time, so short processes which are in a queue are penalized over the longer ones (known as convoy effect).
By using this policy we have no starvation, because each process gets chance to be executed after a definite time.
Turnaround time, waiting time and response time depends on the order of their arrival and can be high for the same reasons above.
There isn't prioritization, so using this policy we cannot force certain processes to be completed first which means that this system has trouble meeting process deadlines.
The lack of prioritization means that as long as every process eventually completes, there is no starvation. 
In an environment where some processes might not complete, there can be starvation since the processes that come next the one which might not complete are never executed.

To enable it and see how FCFS works use this command when compiling xv6:

```
$ make qemu SCHEDFLAG=FCFS
```

## FRR - FIFO Round Robin Algorithm

FRR policy will extend the previous default policy. In this policy the decision of which process will run next will be on a First In - First Out basis.
When a process finishes running for QUANTA time, created or finishes waiting for I/O it is considered to be the last process to arrive and wait for its next turn to run.
This will ensure no starvation of process which come due to process executing for long perid of time.

To enable it and see how FRR works use this command when compiling xv6:

```
$ make qemu SCHEDFLAG=FRR
```


## MLQ - Multilevel Queue Scheduling

This policy will maintain three queues and work according to the following rules:
* The first queue will hold the high priority processes.
* The second queue will hold medium priority processes.
* The third queue will hold low priority processes.
A process with a higher priority will be preferred and run before a process with a medium/lower priority.
This means that no low or medium priority process will be allowed to run while there are still high priority runnable processes (or running), and no low priority process will be allowed to run while there are still medium priority runnable processes.
The processes in the first two queues will be scheduled according to FIFO Round Robin. 
In the last queue processes will be scheduled in a First Come First Served (non-preemptive) manner.

The following system call will change the priority queue of the process with a specific pid process:

```
int chpr(int pid, int priority)
```

In this case ```priority``` is a number between 0 and 2 which represents the new process priority (Deafult priority is 1 for all process except init & sh which have priority 2).

To change the ```priority``` , use following command:
```
$ change pid newPriorty
```
where pid is the process id and newPriority is updated priority (Both are integer values)

To enable Scheduling and see how MLQ works use this command when compiling xv6:

```
$ make qemu SCHEDFLAG=MLQ
```


## Credits

*This project is the work of Aditya Kumar, Raushan Raj, Vineet Kumar,  Parekh Manan Jagdishbhai*

*It is done under supervision of Dr.Bibhas Ghosal Asst. Prof. of IT dept. in IIIT Allahabad*

__Group No: 21__

- IIT2018046  Aditya Kumar
- IIT2018031  Raushan Raj
- IIT2018096  Vineet Kumar
- IIT2018080  Parekh Manan Jagdishbhai


## Acknowledgments

xv6 is inspired by John Lions's Commentary on UNIX 6th Edition (Peer
to Peer Communications; ISBN: 1-57398-013-7; 1st edition (June 14,
2000)). See also http://pdos.csail.mit.edu/6.828/2014/xv6.html, which
provides pointers to on-line resources for v6.

xv6 borrows code from the following sources:
    JOS (asm.h, elf.h, mmu.h, bootasm.S, ide.c, console.c, and others)
    Plan 9 (entryother.S, mp.h, mp.c, lapic.c)
    FreeBSD (ioapic.c)
    NetBSD (console.c)

he following people have made contributions:
    Russ Cox (context switching, locking)
    Cliff Frey (MP)
    Xiao Yu (MP)
    Nickolai Zeldovich
    Austin Clements

In addition, we are grateful for the bug reports and patches contributed by
Silas Boyd-Wickizer, Peter Froehlich, Shivam Handa, Anders Kaseorg, Eddie
Kohler, Yandong Mao, Hitoshi Mitake, Carmi Merimovich, Joel Nider, Greg Price,
Eldar Sehayek, Yongming Shen, Stephen Tu, and Zouchangwei.

The code in the files that constitute xv6 is
Copyright 2006-2017 Frans Kaashoek, Robert Morris, and Russ Cox.

