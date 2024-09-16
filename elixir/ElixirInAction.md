## Elixir In Action, 3rd Edition, by Saša JuriÊ

### Erlang

Erlang is more than a programming language. It's a full-blown development platform consisting of four distinct parts:

- the language,
- the virtual machine,
- the framework (OTP - Open Telecom Platform), and
- the tools.

### High availability

To make systems work 24/7 without any downtime, you must first tackle some technical challenges:

- **Fault tolerance** - A system must keep working when something unforeseen happens. Unexpected errors occur, bugs creep in, components occasionally fail, network connections drop, or the entire machine where the system is running crashes. Whatever happens, you want to localize the effect of an error as much as possible, recover from the error, and keep the system running and providing service.
- **Scalability** - A system should be able to handle any possible load. Scaling the hardware in the case of system demands should be possible without having to modify the system.
- **Distribution** - To make a system that never stops, you need to run it on multiple machines. This promotes the overall stability of the system: if a machine is taken down, another one can take over
- **Responsiveness** - Request handling shouldn’t be drastically prolonged, even if the load increases or unexpected errors happen. In particular, occasional lengthy tasks shouldn’t block the rest of the system or have a significant effect on performance.
- **Live update** — In some cases, you may want to push a new version of your soft- ware without restarting any servers. For example, in a telephone system, you don’t want to disconnect established calls while you upgrade the software.

### Erlang concurrency

The basic concurrency primitve is the **Erlang process**. It is the unit of concurrent execution.

A scheduler is an OS thread responsible for executing multiple Erlang processes. BEAM is asingle OS process. BEAM uses multiple schedulers to parallelize the work over available CPU cores.

### Fault tolerance

Erlang processes are completely isolated from each other. They share no memory, and a crash of one process doesn’t cause a crash of other processes. This helps you isolate the effect of an unexpected error. If something bad happens, it has only a local effect. Moreover, Erlang provides you with the means to detect a process crash and do something about it; typically, you start a new process in place of the crashed one.

### Scalability

Typical Erlang systems are divided into a large number of concurrent processes, which cooperate together to provide the complete service. The virtual machine can efficiently parallelize the execution of processes as much as possible. Because they can take advantage of all available CPU cores, this makes Erlang systems scalable.

### Distribution

Communication between processes works the same way regardless of whether these processes reside in the same BEAM instance or on two different instances on two sep- arate, remote computers. Therefore, a typical, highly concurrent, Erlang-based system is automatically ready to be distributed over multiple machines. This, in turn, gives you the ability to scale out—to run a cluster of machines that share the total system load. Additionally, running on multiple machines makes the system truly resilient; if one machine crashes, others can take over.

### Responsiveness

A scheduler is preemptive—it gives a small execution window to each process and then pauses it and runs another process. Because the execution window is small, a sin- gle long-running process can’t block the rest of the system. Furthermore, I/O opera- tions are internally delegated to separate threads, or a kernel-poll service of the underlying OS is used, if available. This means any process that waits for an I/O oper- ation to finish won’t block the execution of other processes.

### Concurrent Elixir

To make your system highly available, you must tackle the following challenges:

- **Fault tolerance** - Minimize, isolate, and recover from the effects of run-time errors.
- **Scalability** - Handle a load increase by adding more hardware resources without changing or redeploying the code.
- **Distribution** - Run your system on multiple machines so that others can take over if one machine crashes.
