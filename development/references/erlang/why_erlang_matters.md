# Why Erlang Matters

https://sameroom.io/blog/why-erlang-matters/

By Peter Hizalev

Peter is the co-founder of Sameroom and its chief technical officer. This is a re-post of a 2012 paper.

I still remember vividly my first encounter with a SMP computer. This was around 1996 and it was a dual-socket Pentium Pro monster. It blew my mind.

Growing up with the x86 [r]evolution, I was always curious about the innerworkings of computer hardware. My first 8088 board was simple enough that I could understand every signal line in it. Now, this Pentium Pro machine before me had two processors and two sets of L1 and L2 caches, with RAM shared between them. And I was wondering—how was it possible to keep caches and RAM in sync? How would someone actually write software for this incredible beast?

Fast-forward to 2012. Two things happened: the internet and the power wall.

The internet ignited the renaissance of distributed computing. We often cite operations of a scale unprecedented in pre-internet information systems. But this is actually true for just a handful of companies. What really undid traditional information systems is how quickly the capacity requirements grow once а product hits the hockey stick. This made old systems terribly uneconomical. Seemingly overnight, the scale-up approach of expensive, proprietary systems was replaced by scaling-out generic, consumer-level kits.

The power wall killed the great hope that we can keep scaling single-threaded performance forever. While superscalar improvements were still there, CPU designers finally took the SMP revolution to the streets in the mid 1990s, when we witnessed the emergence of multi-socket PC servers. By the 2010s, multiple core CPUs became commonplace, all the way from laptops to smartphones.

A seemingly independent development was the idea of connecting multiple computers with a network and no shared memory. It was years in the making: core theoretical concepts behind distributed computing were developed in the 1970s, with continued research and some commercial applications emerging in the 1980s. In the 1990s, the quickly-evolving Ethernet made local computer networks ubiquitous and "client-server architecture" became the buzzword of the day.

In a distributed system, we have two or more processes talking to each other over a network. They do this by sending each other messages.

A message from process A to process B is copied from the memory of process A, travels over the network wire, and into to the memory of process B.

Now, it seems that in a SMP computer we don’t have the problem with copying data between memories of processes running on different CPUs, since we can share a memory block between threads. Wrong! In a SMP system, when process B reads a memory block written by process A, there is still actual copying going on: from process A’s CPU cache over the interconnect, to process B’s CPU cache.

Cache coherence is the under-the-hood copying mechanism that tries to pretend the copying is not happening, and that memory is really shared.

This copying can get pretty involved if you're running a NUMA system, where a message from process A to B travels over multiple hops in the interconnect—much like a network packet would in a switched data center. The more CPU cores we place on the interconnect, the less it looks like shared memory and more like separate networked computers—cache coherence does not scale.

So here we are, with these awesome systems built out of different flavors of processes talking over the network and pretending to share memory. Hardware threads are used when communication is more frequent or the amount of shared data is substantial. Networked processes are used when scaling requirements are less predictable, or fault tolerance is required.

Some applications exhibit embarrassing parallelism, where we can nicely package computation to saturate a given SMP computer and scale out—and speed up—by adding more computers.

Unfortunately, such applications are rare. Most interactive applications can be represented as graphs of interdependent processes waiting for I/O to complete. This makes it really hard to nicely saturate today’s entry-level SMP computer.

Enter another renaissance: virtualization.

Economical efficiency in mind, we would love to co-locate multiple parts of an application on the same physical computer. The caveat is that these applications may have different, or even conflicting dependencies within an operating system. Virtualization offers full isolation by running many instances of an operating system on a single physical computer. I mention renaissance, because the utility of virtualization was well known before today. Mainframes used virtualization extensively for decades. They simply became way too powerful to run anyone’s single application and it made sense economically to start virtualizing.

Mainframes still exist, but they were largely disrupted by minicomputers in the 1970s and then minicomputers were further disrupted by PC servers in the 1990s.

We can argue that PC servers are becoming too powerful to run distributed systems economically. Much like in the advent of the PC server era, when the economy of scale in personal computers made them superior to the previous generation of computers, today’s smartphones will do the same to the ARM-based servers of tomorrow. At the same time, the speed of local networking will be approaching the speed of CPU interconnect, while the complexity of CPU interconnect will be approaching that of a switched packet network. In the end, this new type of computer will bring about data-centers-on-a-chip or high-density computing, whichever way we look.

What abstractions do we have today to deal with these type of a computer? Processes over a TCP/IP network and threads over shared memory seem like two very different ways of dealing with one problem—communication between concurrent workflows. Both are arguably antiquated for this new computer. TCP/IP was designed for unreliable global networks and carries significant overhead for fast local interconnects. Shared memory does not scale to a large number of CPU cores. But all we really want is to send a message from workflow A to workflow B.

Enter Erlang.

Erlang was designed in the 1980s by group of engineers at Ericsson. They specifically were trying to address the shortcomings of existing languages with respect to handling highly-concurrent telephony applications with extreme reliability requirements. Concurrency meant handling millions of small processes that would occasionally communicate with each other. Reliability meant guarding against hardware failure and, more importantly, against bugs in the program. Erlang designers made a great tradeoff from the outset: immutability and single assignment (very uncommon in conventional programming languages), enforced by the VM.

On top of immutability, the Erlang VM defines lightweight processes that can send and receive messages. This enables the level of isolation for much nicer garbage collection—processes can be garbage collected independently, eliminating the need for a stop-the-world model. Further, Erlang promotes the least amount of error handling in favor of quickly crashing processes. To address error recovery, Erlang defines the concept a supervision tree—a hierarchy of watchdog processes whose only responsibility is to restart failed worker processes. Another key built-in construct is the ability to send and receive messages between processes on VMs running on connected computers, and yet another is the ability to hot-load code without stopping the VM, or any running process.

I would argue that the Erlang execution model is very well suited to run directly on a future data-center-on-a-chip computer without an operating system and virtualization. Immutability removes the need for cache coherence (although signaling inside the VM may need one, or some sort of specialized hardware). The Erlang VM already maps one process scheduler to one hardware thread. Processes are load-balanced to evenly saturate the available CPU cores and maintain cache locality. Messages are precisely what they are—immutable memory blocks shuttled between caches over a switched interconnect. Supervision trees can span a hardware topology and handle both hardware failures and software bugs for great fault tolerance.

It would likely take a very evolved Erlang to run on this computer of the future. It may well be the Erlang VM that hosts other—non Prolog-based—languages on top of it, while enforcing all the important semantics (Elixir is a good candidate). Or, it may be some new software altogether.

Erlang matters today because it demonstrates how these semantics can be elegantly packaged in one language, execution model, and virtual machine.
