---
title: "Java Concurrency in Practice: Reading through the book"
sub_title: "Chapter 1"
date: 2020-12-27T17:10:41-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/trains_compressed.jpg"
alt_text: "Java Concurrency in Practice screenshot"
year: "2020"
month: "2020/12"
draft: false
categories:
  - Career
tags:
  - concurrency
  - Java
  - book
series:
Summary: "This book, though written during the Java 5 time frame and released in 2006, is a must read for anyone looking to master multi-threading on the JVM environment. Even today, it's recommended on forums and websites as a book that will help you to understand proper Java threading techniques."
---

{{<tab>}} The first chapter is more than just an introduction to the book. While it doesn't give a lot of specific detail, it begins to lay out the framework the rest of the book will use in order to ensure your programs are both correct and lively. To me, the intro does seem to downplay the benefits of non-block IO operations, it does at least mention them explaining there are benefits:

{{<sand-quote
"If an application goes to read from a socket when no data is available, read blocks until some data is available. In a single-threaded application, this means that not only does processing the corresponding request stall, but processing of ALL requests stalls while the single thread is blocked. To avoid this problem, single-threaded server applications are forced to use nonblocking I/O, which is far more complicated and error-prone that synchronous I/O. However, if each request has its own thread, then blocking does not affect the processing of other requests."
"-Java Concurrency in Practice, page 4."
>}}

{{<tab>}} The book goes further explaining that *newer*, remember this book is from 2006, operating systems are overcoming the limitation of small amounts of thread pools being available; however, I think it's important to remember the radical change that NodeJS brought about with it. While not a new idea, Node brought about a new wave of single threaded and more efficient design into the web. Now we also have Reactive Streams in Java [Java 9 Flow API](https://docs.oracle.com/javase/9/docs/api/java/util/concurrent/Flow.html), that are an initiative from several different vendors to bring about a more efficient use of resources to the JVM.[Project Loom](https://wiki.openjdk.java.net/display/loom/Main) is another huge change related to threading that will hopefully come to the JVM sooner rather than later, and has the ability to radically change the way people handle IO inside of Java.

{{<br>}}

{{<tab>}} From this, we continue on to notes about safety and the risks of a true multi-threaded environment. Section 1.3.1 (Safety hazards) introduces the concept incorrect synchronization. The example shows two different threads trying to update a variable; while incrementing a variable may appear to be a single statement it is in fact 3 actiions: read variable, increment in memory, modify variable.

```Java
int i = 3;
i++ // While this is one line, the actual processor instructions will break down to multiple calls so this is not an atomic operation
System.out.println(i);
```

With this in mind, we can better understand that multiple threads trying to act on a single variable can have conflicting results *sometimes*. This *sometimes* is important, because the problem with multi-threaded concurrency bugs is they're often subtle and hard to trace without realizing the exact nature of the problem. If we have two threads trying to update a shared variable, then we have two threads that must read, modify, then update the variable data so if there's no synchronization both variables could read the original value and increment the value. So we can have two threads that add one to a variable only to have 1 and not 2 be added to the value of the shared variable.
||||||
|-|--|--|-|-|-|
| thread 1 reads 3(i) | {{<tab>}} |{{<tab>}} thread 1 updates in memory to 4(i)|{{<tab>}} | thread 1 writes 4| {{<tab>}}|
| {{<tab>}} | Thread 2 reads 3(i) |{{<tab>}} thread 2 updates in memory to 4(i)|{{<tab>}} | {{<tab>}}| thread 1 writes 4|

{{<tab>}}-- A better example of this can be seen on page 6 of the book, but from this small example you can see how two threads can interact with the same memory and come to the wrong result.

This is also an important point of the book, no matter if your program works most of the time or all of the time; if you have a logic bug in your program related to threading, then your program is incorrect and wrong and you just haven't experience the effects *YET*. You may ask why this can happen, where you can have programs that work correct most or all of the time but still be wrong. One big source of problems can come from the very nature of the Java environment itself and the written guarantees of the language and it's usage. For example:
{{<sand-quote
"In the absence of synchronization, the compiler, hardware, and runtime are allowed to take substantial liberties with the timing and ordering of actions, such as caching variables in registers or processor-local caches where they are temporarily (or even permanently) invisible to other threads. These tricks are in aid of better performance and are generally desirable, but they place a burden on the developer to clearly identify where data is being shared across threads so that these optimizations do not undermine safety."
"pg.7"
>}}

{{<tab>}} When thinking about some of the subtle problems that come from shared state, this statement highlights a couple major issues. If we have no guaranteed ordering then we can't say what should come first and so we can have unexpected behavior when we expect our logic to flow in a specific order. Additionally, what about shared values? The 'invisible to other threads' means that we could have a shared variable that we need to share logic or state inside of our application and if we don't ensure that we're doing the proper things then we can end up with programs that don't even know if there was a change. This means we could have a __ready__ flag in our application on a shared variable, but if we don't let the compiler know then any thread's update could be confined to that thread alone. So we could waste a lot of resources performing background operations only to have the state be unchanged on other threads.

{{<br>}}

{{<tab>}} The last section of chapter one __1.4 Threads are everywhere__ also explains how we cannot guarantee that our code is a single thread anymore since libraries and frameworks can themselves be using multiple threads; therefore, it's no longer a choice to have *correctly* threading programs because without proper thread management our programs are broken.

