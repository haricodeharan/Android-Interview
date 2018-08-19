# Notes later to be arranged

## [**Difference between a Program, a Process, a Task and a Thread?**](http://web.archive.org/web/20100807021758/http://kquest.co.cc/2010/03/program-process-task-thread/)

What is a Program?

- A program is an [executable file](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Executable) residing on the disk (secondary storage) in a directory.
- Hence, program is also termed as a set of instructions stored in the secondary storage device that are intended to carry out a specific job.
- It is read into memory and executed by the kernel.
- Therefore, a program is termed as a &#39;passive entity&#39; which exists in the secondary storage even after the machine reboots.
- Example:
- On a Microsoft Windows® system: The &#39;Calculator&#39; executable that is usually stored at &quot;\&lt;drive\&gt;:\windows\system32\calc.exe&quot;.
- On a [Linux](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Linux) system: The &#39;ls&#39; binary that is normally stored at: &quot;/bin/ls&quot;.

What is a [process](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Process_%28computing%29)?

- An executing instance of a program is called a process.
- Some [perating systems](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Operating_system) use the term &#39;task&#39; to refer to  a program that is being executed.
- A process is always stored in the [main memory](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Computer_data_storage) also termed as the primary memory or [random access](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Random-access_memory) memory.
- Therefore, a process is termed as an active entity. It disappears if the machine is rebooted.
- Several process may be associated with a same program.
- On a multiprocessor system, multiple processes can be executed in parallel.
- On a uni-processor system, though true parallelism is not achieved, a process [scheduling](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Scheduling_%28computing%29) algorithm is applied and the processor is scheduled to execute each process one at a time yielding  an illusion of concurrency.
- Example:
- Executing multiple instances of the &#39;Calculator&#39; program. Each of the instances are termed as a process.

What is a [thread](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Thread_%28computer_science%29)?

- A thread is a subset of the process.
- It is termed as a &#39;lightweight process&#39;, since it is similar to a real process but executes within the context of a process and shares the same resources allotted to the process by the kernel (See [http://kquest.co.cc/2010/03/operating-system](http://web.archive.org/web/20100807021758/http://kquest.co.cc/2010/2010/03/operating-system/) for more info on the term &#39;kernel&#39;).
- Usually, a process has only one thread of control – one set of machine instructions executing at a time.
- A process may also be made up of multiple threads of [execution](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Execution_%28computing%29) that execute instructions concurrently.
- Multiple threads of control can exploit the true parallelism possible on multiprocessor systems.
- On a uni-processor system, a thread scheduling algorithm is applied and the processor is scheduled to run each thread one at a time.
- All the threads running within a process share the same [address space](http://web.archive.org/web/20100807021758/http://en.wikipedia.org/wiki/Address_space), file descriptor, stack and other process related attributes.
- Since the threads of a process share the same memory, synchronizing the access to the shared data withing the process gains unprecedented importance.
