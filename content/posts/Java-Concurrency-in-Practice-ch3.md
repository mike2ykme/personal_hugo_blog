---
title: "Java Concurrency in Practice: Chapter 3 part 1"
sub_title: "Sharing Objects: The volatile keyword"
date: 2021-01-14T21:44:52-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/trains_compressed.jpg"
alt_text: "Java Concurrency in Practice Ch3 screenshot"
year: "2021"
month: "2021/01"
draft: false
categories:
- Career
tags:
- Java
- concurrency
- book
series:
Summary: "Reading and taking notes on 'Java Concurrency in Practice' Chapter 3"
---
Resources:
* [Java Language Specification: Java SE15.](https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html)

DISCLAIMER: I'm neither smart a nor a *clever* person. I would highly recommend you get this book and study it instead of the JLS, and while I'm including links from the JLS I don't want to claim that I understand anything better than the author of *Java Concurrency in Practice*. Indeed, I'm writing all of this so I can better understand Java's memory model and have found myself using the JLS docs first out of curiosity and then out of respect for how the author took little pieces of information and boiled them to a truly understandable format. Anyways, get the book and read it several times. 

{{<br 2>}}

{{<tab>}} The main point of this chapter deals with visibility of objects and how this visibility is propagated throughout the JVM's memory and how the internal workings of the Java platform can cause unexpected behaviors. We are also warned there very structure of our code, or how it's laid out in source format does not provide any guarantee to how it will actually be executed during runtime. So what kind of unexpected behaviors can happen with this kind of unexpected results or behavior? From the book there is the following example that actually provides a lot of information just from the example

{{<br>}}

```Java
public class NoVisibility{                      // I can't put the 'yuck face' from the book here :(
    private static boolean ready;
    private static int number;

    private static class ReaderThread extends Thread{
        public void run(){
            while(!ready)
                Thread.yield();
            System.out.println(number);
        }
    }

    public static void main(String[] args){
        new ReaderThread().start();
        number = 42;
        ready = true;
    }
}
```
* page 34

{{<br>}}

Breaking down the example:
* We have a couple primitive variables that will be shared between two threads, but it's important to note there's no synchronization on these elements.
* We create a reader thread that will run until the boolean ready is true
* We start a thread, set the value of *number*, then set the value of *ready* to true.
  * Because there's no synchronization, the JVM may make changes to our layout based on it's rules as long as it won't break what would be expected to happen in a single thread program.

{{<br>}}

Let's start with a huge point, there's no synchronization on this object state values so Java does not have to guarantee they'll be run in any specific order. While in the code we see the reader thread is created and then started BEFORE the number and ready variables are adjusted, this is NOT guaranteed. Java is free to update the variables before the thread starts. While this change may make the ReaderThread never loop, there's also another side to this. There's no guarantee that ReaderThread will ever retrieve the new values. 
* There is a chance this could run as we expect with ReaderThread printing out the number value; however, this is not guaranteed. 
* The JVM could reorder this code and during optimization prevent or delay the reading and writing the 'number' and 'ready' variables from the shared memory. Instead, they could be reading a cached version that WILL NEVER BE UPDATED.

{{<br>}}
We also cannot hope that Java's Thread.yield() method will have any help here:

{{<sand-quote
"It is important to note that neither Thread.sleep nor Thread.yield have any synchronization semantics. In particular, the compiler does not have to flush writes cached in registers out to shared memory before a call to Thread.sleep or Thread.yield, nor does the compiler have to reload values cached in registers after a call to Thread.sleep or Thread.yield."
"https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html#jls-17.3"
"17.3. Sleep and Yield"
>}}

Perhaps this is not surprising, but for a process that is designed to help facilitate the inter-process communication on waiting and resuming tasks or acquiring locks it provides no synchronization on it's memory.

So what does all this mean for our program?

{{<tab>}} It means that our example program above is broken, and regardless of whether it works "Most of the time" it's incorrect.
It also means that we cannot simply 'assume' or expect properly putting syntax in the correct position will work either, the JVM has the ability to move code around and to work with internal cache, and per the book the ability to make these changes is all in the benefit of making the Java platform run faster and produce more efficient code. If the JVM can monitor variables and see their access and update values it can remove unnecessary writes and reads from a loop or perform other similar behaviors.



{{<sand-quote 
"It should be noted that the presence of a happens-before relationship between two actions does not necessarily imply that they have to take place in that order in an implementation. If the reordering produces results consistent with a legal execution, it is not illegal. For example, the write of a default value to every field of an object constructed by a thread need not happen before the beginning of that thread, as long as no read ever observes that fact. "
"https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html#jls-17.4.5"
"Java15 JLS 17.4.5"
>}}

{{<br>}}

Does this mean that our program will never write memory back to the main memory?

{{<tab>}} No, but it does mean that without any kind of synchronization, and Thread methods like yield() & wait() do not provide any guarantee. It also leads us to another problem that the book explains 'Stale Data'. We have no guarantee of when the last value was updated, we can't even tell if we're working with main memory or just a cached version that's local to our thread, and with each additional thread this problem is compounded because we can have varying degrees of inaccuracy. 

So what can we do about this caching problem?

Java has a keyword in place to ensure that we always retrieve the most current version of the variable stored in memory. It's the *volatile* keyword. Here's what the docs have to say about it:

{{<raw>}}

<div class="w3-panel w3-leftbar w3-sand">
  <span style="font-size: 150px; line-height: 0.6em; opacity: 0.2">❝</span>
  <div class="w3-large w3-serif" style="margin-top: -40px">

<!--  -->

The Java programming language allows threads to access shared variables (<a href="https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html#jls-17.1">§17.1</a>). As a rule, to ensure that shared variables are consistently and reliably updated, a thread should ensure that it has exclusive use of such variables by obtaining a lock that, conventionally, enforces mutual exclusion for those shared variables.
{{<br 2>}}
The Java programming language provides a second mechanism, volatile fields, that is more convenient than locking for some purposes.
{{<br 2>}}
A field may be declared volatile, in which case the Java Memory Model ensures that all threads see a consistent value for the variable (<a href="https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html#jls-17.4">§17.4</a>). 
<!--  -->

  </div>
  <a href="https://docs.oracle.com/javase/specs/jls/se15/html/jls-8.html#jls-8.3.1.4" style="text-decoration: none">
    <p>8.3.1.4. volatile Fields <img src="/icons/link.svg" alt="link icon"></p>
  </a>
</div>

{{</raw>}}


OK! So we have a "...more convenient than locking for some purposes." variable, what does that mean? 

This means that when it comes to actions regarding this variable, we are able to ensure that we're always reading and writing to the main memory and therefore will always have the most current value; however, this value is not guaranteed to stay at it's current value as it has no locking and therefore another thread may modify it.
* So can one thread read while another thread write to the value? 

Luckily for us, and with the exception of 64 bit operators *Long* and *Double*, we are guaranteed that reading or writing to a variable cannot interfere with another thread reading or writing to a variable [17.6. Word Tearing ](https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html#jls-17.6). So we can be assured that our change will [*happen-before*](https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html#jls-17.4.5) any subsequent reading or writing of the variable. Just to note, I've intentionally used this phrasing, as the JLS uses wording like this to describe how its memory model should operate.

So we know the memory won't be corrupted or written in half, with two exceptions, but can we really use this keyword to ensure synchronization? No, this keyword provides no real synchronization guarantee except for the guarantee that comes from ensuring caching on local threads will be read/written directly to memory so we don't have to worry about updated not being propagated through the JVM memory. 


What does this mean?

Remember the __while (!ready)__ from the book's earlier example? Now we can guarantee that if there is a change to the variable, this change will not be isolated to a single thread, but instead the changes will be visible to the entire program and the earlier code will be able to continue as we would normally expect in a single threaded environment. Also, while __volatile__ variables are seen correctly there's still no coordination so you cannot guarantee the value of a variable will remain consistent while you process other information. Therefore, a __volatile__ variable makes a great flag for state indication (open/closed/ready) but would make a poor choice if you're looking to share an actual data that might be used for processing other information.
