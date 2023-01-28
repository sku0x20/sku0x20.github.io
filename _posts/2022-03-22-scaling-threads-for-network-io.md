---
layout: post
title:  "Scaling Threads for Network io"
date: 2022-03-22
categories: multithreading
upload-date: 2023-01-28
---

Scaling threads for Network io

===== Problem =====
simulating n hubs
we can think of a hub as a udp socket which can send, receive and reply to packets.
i find this to be the same problem as c10k.

===== understanding the problem =====
there are so many things going on here and i might be wrong at some places
for further research and going in depth one can always look at the links provided below. 

let's try to make sense of all this step by step.
let's separate things into topic
1. io and threading at kernel level
    1.1 native threads
    1.2 io
        1.2.1 blocking (makes the whole thread sleep till we receive packet/data, implemented with interrupt/signal)
        1.2.2 non-blocking (we have to poll, if we have received anything) (epoll_wait() can be used with multiple file descriptors, more on that below)
        1.2.3 async (aio)
2. io and threading at java/jvm level
    2.1 threading model
        2.1.1 threads maps to native threads (one to one mapping)
        2.1.2 threadpool 
        2.1.3 forkjoinpool
    2.2 io
        2.2.1 blocking io
        2.2.2 non-blocking io
        2.2.3 async io
    2.3 project loom
3. kotlin coroutines

==== os level ====
== threads ==
we don't want to create new threads, creaeting a thread is a fairly expensive. (syscalls)
    we want to create threads strictly.
context switching is another thing that should be avoided.
    thread context switching means loading another thread for execution and unloading/loading cpu registers. kernel or os does this.
        this is an overhead. we want to minimize this.
waking up a thread from sleep is expensive too
    not fully sure what parked/suspended threads means at kernel/os level, if it's just context switching that's expensive too

== blocking ==
blocks the whole thread or more like suspends the whole thread and kernel/os wakes it up when data is received.
    - with more sockets more threads are needed => more thread wake ups => more context switching

== non blocking io ==
with non-blocking io, one has to keep polling or while looping if data is available and then process it.
    - this wastes cpu cycles. which is not what we want, definitely we can loop over multiple sockets add a wait but still cpu cycles are wasted
    - a better alternative is epoll() in linux
if i understood it correctly, i know why epoll is favoured over interrupt or signal or callbacks
    - epoll is the same thing with much control and user/client can easily create a multithreaded api on top of it.
    - and if i understand this correctly kernel have to do scheduling in case of aio. (below)
    - and we can implement this kind of thing by ourselves. it's just building another abstraction.
        - when a socket is ready to read we can read and create new thread for processing that data,
            or we can put that to some events queue like node does or something.
    - am i not sure. but maybe that's why they(node and other) choose to use epoll with event loop
        - easy to understand control flow

== aio ==
Problem with AIO is that your callback function code runs on the system thread and so on top of the system stack.
A few problems with that as you can imagine. (https://stackoverflow.com/a/5845109)

== conclusion ==
blocking io => with more sockets more threads are needed => more thread wake ups => more context switching
aio => https://stackoverflow.com/a/5845109
non-blocking io with optimized polling(epoll) => best solution, can make out own async layer on top of it. (that's what nginx and other programs did)

we can minimize thread count by something called threadpool, and issuing tasks to complete to each thread. 
    but with blocking this won't work as it will block the whole thread => and due to this the thread count in threadpool will increase with each socket.
actually thread-pool are implemented in many languages.
also, there is this concept of event loop, like in node, using only one thread.



==== java/jvm level ====
== threads ==
java threads maps directly native threads, so we can see they have the same disadvantages as native threads(above).
threadpool => pool of threads with can we give tasks for execution.
forkjoinpool => better optimized than threadpools and uses something called work-stealing algorithm. have its own dispatcher, which chooses which thread to give this task,
                form basis of kotlin coroutine (or that's what i think)

== io ==
blocking io => suffers from same disadvantages as native blocking io. 
    - more sockets => more threads => more context switching
non-blocking io => does not block but one has to check or poll if data is available
    - java has something called selector in nio package which behaves like epoll_wait(). it stops the need for polling
    - idk, how it's implement by java though or how many channels one selector can support.
async io => only available for stream(tcp).
    - i wouldn't have to get into any of these, if AsynchronousUDPSocketChannel is available. lol...


== project loom ==
project loom has something call fibers which are light version of thread.
they do not directly map to native threads.
and yup how does blocking io works with them? blocking io is replaced with non-blocking version.(more info in links)



==== Kotlin Coroutines ====
loosely based on or better word is inspired from forkjoinpool.

== working ==
less threads are created

what i think happens is there is runtime level task switching rather than native thread level context switching
    which is cheaper. so no stack manipulation or anything. meaning less overhead.
    
native thread context switching is heavy but what coroutines are doing is
    - there are a number of tasks to be executed
    - a thread can execute multiple tasks
    - we create multiple tasks and schedule them to be executed by any thread if available
        - work stealing algorithm and like deque and other black magic stuff
    - like tasks are given to thread, those tasks can be suspended and resumed,
        - no need for thread context switching as now same thread can execute multiple tasks
            (runtime is providing this functionality)
        - so less overhead of context switching at os level by os which is heavy
            - because it needs to save and restore the whole call stack.

== problem with blocking io ==
what's happening with blocking is the whole thread is getting blocked, so now it can't execute the other tasks

== comparison to forkpooljoin ==
in kotlin coroutine we can write better code with easy to understand control flow.(structured concurrency)
and as they claim it has better dispatcher/scheduler than forkpooljoin (it was initially using that only)

== limited parallelism ==
limited parallelism does not mean limited thread, it's simply another dispatcher which limits the number of concurrent tasks in that dispatcher(not threads)
Dispatchers.IO is UnlimitedIoScheduler under the hood and can span unlimited threads.
    - so Dispatchers.IO.limitedParallelism(2000) can create up to 2000 threads 
    - then how's its different from CachedThreadPoolDispatcher(), Io dispatcher is more optimized like joinforkpool is than simple threadpool
        like using work stealing algorithms and other black magic, so fewer threads are created (we want fewer threads)
default Dispatcher.IO is limitedParallelism(64)

==== Conclusion ====
i think going on with Selector from nio package is the best approach.
we can also build a async api on top of that if required.

== other library approaches ==
using netty => support for event driven io
using mina
project loom

== other less meaningfull/incomplete/bad approaches ==
- use something like event loop.
    - like, we append socket created to a queue in simulated hub function
    - then, we like loop through this queue and blocking receive
    - see, the socket which will send the packet first should receive the packet first
        - if it receives a packet later on we might suffer.
    - also another thing is we can just like give a timeout for each receive, and then yield
        receive within that timeout.
- same as above with just non-blocking thing, waiting for timeout. if we receive something we process it else we move past it
- same as above but with no queue but rather inside coroutines, and delay and then process and then loop.
- using yield with nio non-blocking
    - it's actually same as a for loop just with context switching overhead

== other important notes ==
"The general concept of asynchronous only implies that the process continues while the background operation is performed,
 the mechanism by which this occurs is not specific" ~ https://stackoverflow.com/a/40049018
a library which is saying it's an async can be blocking altogether just using threads within itself and hiding that thread
from client's view, it is async right... or worse using non-blocking and looping, who knows...


==== Links ====


== os/linux ==
http://www.kegel.com/c10k.htm
https://blog.codecentric.de/en/2019/04/explain-non-blocking-i-o-like-im-five/
https://thetechsolo.wordpress.com/2016/02/29/scalable-io-events-vs-multithreading-based/
https://stackoverflow.com/questions/2777192/how-does-linux-blocking-i-o-actually-work
https://lwn.net/Kernel/LDD3/
http://byteliu.com/2019/05/10/Linux-The-block-I-O-layer/
https://www.oreilly.com/library/view/linux-device-drivers/0596000081/ch05s02.html
https://jvns.ca/blog/2017/06/03/async-io-on-linux--select--poll--and-epoll/
https://stackoverflow.com/questions/22662806/linux-interrupt-vs-polling
https://en.wikipedia.org/wiki/Asynchronous_I/O#:~:text=Polling%20provides%20non%2Dblocking%20synchronous%20API%20which%20may%20be%20used%20to%20implement%20some%20asynchronous%20API
https://en.wikipedia.org/wiki/Asynchronous_I/O
https://www.reddit.com/r/kernel/comments/q4jwip/comment/hfzh7ep/?utm_source=share&utm_medium=web2x&context=3
https://lkml.indiana.edu/hypermail/linux/kernel/0305.2/0697.html
https://stackoverflow.com/questions/17593699/tcp-ip-solving-the-c10k-with-the-thread-per-client-approach
https://www.google.com/search?q=C10k+epoll
https://en.wikipedia.org/wiki/C10k_problem
https://www.google.com/search?q=c10k+problem
https://www.google.com/search?q=linux+kernel+vs+epoll_wait+not+interupts
https://stackoverflow.com/a/5845109
https://www.google.com/search?q=linux+aio+vs+epoll
https://stackoverflow.com/questions/5844955/whats-the-difference-between-event-driven-and-asynchronous-between-epoll-and-a
https://stackoverflow.com/questions/13407542/is-there-really-no-asynchronous-block-i-o-on-linux/57451551#57451551
https://news.ycombinator.com/item?id=17529361
https://stackoverflow.com/questions/5440128/thread-context-switch-vs-process-context-switch


== java ==
https://stackoverflow.com/questions/25099640/non-blocking-io-vs-async-io-and-implementation-in-java
https://liakh-aliaksandr.medium.com/java-sockets-i-o-blocking-non-blocking-and-asynchronous-fb7f066e4ede
https://stackoverflow.com/a/22178582
https://stackoverflow.com/a/40049018
https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html
https://mail.openjdk.java.net/pipermail/nio-dev/2013-December/002467.html
https://stackoverflow.com/questions/29482744/why-java-nio2-cant-listen-a-udp-port
https://docs.oracle.com/javase/8/docs/api/java/nio/channels/AsynchronousSocketChannel.html
https://docs.oracle.com/javase/8/docs/api/java/nio/channels/DatagramChannel.html
https://docs.oracle.com/javase/8/docs/api/java/net/DatagramSocket.html#getChannel--
https://stackoverflow.com/questions/592303/asynchronous-io-in-java
https://openjdk.java.net/projects/nio/presentations/TS-5686.pdf
https://anmolsehgal.medium.com/non-blocking-io-java-nio-b18e53a92bad
https://stackoverflow.com/questions/25099640/non-blocking-io-vs-async-io-and-implementation-in-java
https://stackoverflow.com/questions/4427398/java-threads-vs-os-threads
https://medium.com/@unmeshvjoshi/how-java-thread-maps-to-os-thread-e280a9fb2e06
https://www.developer.com/design/an-introduction-to-jvm-threading-implementation/
https://cs.nyu.edu/~jcf/classes/g22.3033-007_sp01/handouts/g22_3033_h52.htm
https://en.wikipedia.org/wiki/Green_threads
http://tutorials.jenkov.com/java-concurrency/costs.html
http://tutorials.jenkov.com/java-nio/nio-vs-io.html
https://dzone.com/articles/thousands-of-socket-connections-in-java-practical
https://www.javacodegeeks.com/2016/08/scaling-thousands-threads.html
https://www.javaadvent.com/2015/12/the-importance-of-tuning-your-thread-pools.html
https://blog.codecentric.de/en/2019/04/explain-non-blocking-i-o-like-im-five/
https://thetechsolo.wordpress.com/2016/02/29/scalable-io-events-vs-multithreading-based/
https://stackoverflow.com/questions/38836279/threading-model-for-high-capacity-java-server
https://stackoverflow.com/questions/2828447/using-threads-to-handle-sockets
https://www.baeldung.com/java-fork-join
https://www.baeldung.com/java-completablefuture
https://stackoverflow.com/questions/700072/java-socket-programming-does-not-work-for-10-000-clients
https://stackoverflow.com/questions/7926864/how-is-the-fork-join-framework-better-than-a-thread-pool
https://stackoverflow.com/a/5483105


== project loom ==
https://www.baeldung.com/openjdk-project-loom
https://stackoverflow.com/questions/70174468/project-loom-what-happens-when-virtual-thread-makes-a-blocking-system-call
https://inside.java/2021/05/10/networking-io-with-virtual-threads/
https://blogs.oracle.com/javamagazine/post/going-inside-javas-project-loom-and-virtual-threads
https://www.google.com/search?q=java+project+loom+blocking+io
https://stackoverflow.com/questions/70174468/project-loom-what-happens-when-virtual-thread-makes-a-blocking-system-call
https://inside.java/2021/05/10/networking-io-with-virtual-threads/


== coroutines ==
https://stackoverflow.com/a/54034684
https://stackoverflow.com/questions/53736127/how-to-implement-nio-socket-client-using-kotlin-coroutines-in-java-code
https://github.com/Kotlin/kotlinx.coroutines/issues/601
https://github.com/Kotlin/kotlinx.coroutines/blob/87eaba8a287285d4c47f84c91df7671fcb58271f/integration/kotlinx-coroutines-nio/src/Nio.kt
https://stackoverflow.com/questions/55756034/understanding-coroutine
https://stackoverflow.com/a/58402108
https://stackoverflow.com/questions/59039991/difference-between-usage-of-dispatcher-io-and-default
https://github.com/Kotlin/kotlinx.coroutines/issues/2943
https://github.com/Kotlin/kotlinx.coroutines/issues/2919
https://github.com/Kotlin/kotlinx.coroutines/issues/261
https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/limited-parallelism.html
https://github.com/Kotlin/kotlinx.coroutines/issues/2919#issuecomment-1000487181
https://kotlinlang.org/docs/coroutine-context-and-dispatchers.html#unconfined-vs-confined-dispatcher
https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-scope/index.html
https://elizarov.medium.com/coroutine-context-and-scope-c8b255d59055
https://github.com/Kotlin/kotlinx.coroutines/pull/633
https://discuss.kotlinlang.org/t/commonpool-default-for-coroutines/11965
https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/limited-parallelism.html
https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/limited-parallelism.html


== network io libraries ==
https://stackoverflow.com/questions/1637752/netty-vs-apache-mina
https://stackoverflow.com/questions/19537547/is-apache-mina-dead-23-10-2013


== others ==
https://www.toptal.com/back-end/server-side-io-performance-node-php-java-go
https://en.wikipedia.org/wiki/Semaphore_(programming)
https://en.wikipedia.org/wiki/Cigarette_smokers_problem
https://en.wikipedia.org/wiki/Spurious_wakeup
https://en.wikipedia.org/wiki/Green_threads
https://blog.codecentric.de/en/2019/04/explain-non-blocking-i-o-like-im-five/
https://thetechsolo.wordpress.com/2016/02/29/scalable-io-events-vs-multithreading-based/


=== update ===
i haven't even touched shared memory/critical sections in this, but things already got so complicated.
with synchronization, locks, semaphores, etc. coming in picture, things will blow up pretty fast.
Multithreading is a vast subject…