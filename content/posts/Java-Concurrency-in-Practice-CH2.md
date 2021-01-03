---
title: "Java Concurrency in Practice: Chapter 2"
sub_title: "Reading through the book"
date: 2021-01-02T23:09:52-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/trains_compressed.jpg"
alt_text: "Java Concurrency in Practice chapter2 screenshot"
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
Summary: "Continuing to read through the book 'Java Concurrency in Practice'"
---

This chapter is called {{<underline>}}Thread Safety{{</underline>}}, and it begins with one of the most helpful comments on ensuring thread safety that has been explained yet, sure it's only the 2nd chapter but I want to highlight its importance:


{{<sand-quote
"Whether an object needs to be thread-safe depends on whether it will be accessed from multiple threads. This is a property of how the object is used in a program, not what it does. Making an object thread-safe requires using synchronization to coordinate access to its mutable state; failing to do so could result in data corruption and other undesirable consequences."
"-Java Concurrency in Practice, page 15."
>}}

Let's look at this quote to better explain things
* If an object isn't going to be accessed by multiple threads then it doesn't need to be thread safe. While this may seem to be controversial or at least at odds with the chapter, it's important to understand that not all objects are intended to be accessed at the same time or to have shared mutable state. You can think of method level declared local variables. Unless you share these variables outside of the method scope, they'll be private to the method and the thread running it; therefore, if they're not meant to be accessed by multiple threads then there's no reason to make them thread-safe, as the additional overhead won't return any value.
* Thread safety is dependent upon usage of an object NOT on an object's actions. Determining whether an object is thread safe cannot be done simply by looking at what kind of task the object performs, instead you must review the actual usage of the object to determine thread-safety. For example, if you have an unsafe class but when used properly or when used in a locally scoped manner it will not matter that the object isn't thread safe
* Thread safety is directly tide to the MUTABLE STATE of an object, and this reflects all mutable state and not simply one element of mutable state. Any mutable variables inside of an object must as a whole be considered part of the mutable state and having immutable objects used as part of a mutable state does not make the state thread safe. Immutable, or stateless, objects are themselves are considered thread-safe in isolation; but using multiple thread-safe objects together, in a mutable fashion, form a single state. Access to the combined state variables must be coordinated or there is no guarantee on the outcome of events because there are no guarantees on accessing and updating arrangements of the individual state elements.

Finally, let's look at a working definition for a thread-safe class:

{{<sand-quote
"A class is thread-safe if it behaves correctly when accessed from multiple threads, regardless of the scheduling or interleaving of the execution of those threads by the runtime environment, and with no additional synchronization or other coordination on the part of the calling code."
"-Java Concurrency in Practice, page 15."
>}}

With this definition, we can continue into our understanding of creating a proper thread-safe class.

*I think it's important to note that we're talking about thread safe classes in a code sense, but safety of a class is related to state and therefore access to state is what we have to manage.*


With section 2.2 {{<underline>}} Atomicity {{</underline>}} it's all about handling state and how using multiple state variables affects the design. The book has an example that is initially stateless (& therefore thread-safe) and introduces state into it. It's through this example that we see some important design and usage techniques. As is the name of the chapter, atomicity is an important point when it comes to protecting state variables. When there are multiple state variables that need to be coordinated, access to any of the must be done in an atomic operation. You cannot think of reading data as an exception either, as reading a variable must be done atomically as any change to state that's not done atomically in respect to reads can lead to data races. So if you need to update or change an object's state based on another part of the object's state then you must do both operations as a single unit of work, or as an atomic operation. 
* **Atomic operations are operations who are completed as one step, this can be a compound action with multiple actions occurring as one unit.**
* Operations similar to *check-then-act* or *read-modify-write* need to be treated as an atomic operation because their results are dependent upon the current state of an object. 

```Java
public class ClassA{
    public int lastNum = 0;
    public int cache = 0;


    public int factorNumber(int factor){
        synchronized(this){      // Inside of a synchronized block we validate state variables
            if(factor == lastNum){  // We perform a check-then-act
                return cache;
            }
        } // If we don't have the number cached, then we go outside of the method to perform a calculation so we don't block other threads.
        int result = expensiveOperation(factor);
        synchronized(this){         // Inside this block we update our state variables
            lastNum = factor;
            cache = result;
        }
        return result;
    }
}
```

*as a side note, I've personally found Rust's [ownership](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html) model to be a great way of thinking about concurrency and ensuring no data races.*

Now that the book has mentioned we need to perform multiple operations in a single step how are we supposed to do this? That's what {{<underline>}} Locking {{</underline>}} is for.
* Java's intrinsic locking, locking built into the languages, is the **synchronized** keyword and it can be used on any object. When used on non-static methods uses the implicit (this) object.
    * **This is a reentrant style lock**, this means if a thread already owns the lock then it can enter/re-enter other code blocks or methods that are guarded by that lock. Upon completely leaving these *synchronized* blocks, the lock will be removed and other threads can access the lock. 

```Java
public class Widget{
    public synchronized void doSomething(){
        ...
    }
}

public class LoggingWidget extends Widget(){
    public synchronized void doSomething(){
        System.out.println(toString() + ": calling doSomething");
        super.doSomething();
    }
}
```
* Listing 2.7. Code that would deadlock if intrinsic locks were not reentrant
* Source: page 27.

The above is using the synchronized keyword, and since it does not list anything to lock on it uses the implicit this of the object. So any method that is protected with the 'synchronized' keyword will use the object's own object as the lock; however, this is a default and you can lock on a different object.

```Java
public class MyClass{
    String myString = "ABC";

    public void doSomething(){
        sychronized(myString){ // locking on another state reference variable
            ...
        }
    }
}

public class MyOtherClass{
    public void doSomethingElse(){
        synchronized(this) { // explicit use of the 'this' object to lock on
            ...
        }
    }
}
```

The last of the locking chapters discusses the basics of efficient locking.
{{<sand-quote
"Avoid holding locks during lengthy computations or operations at risk of not completing quickly such as netowrk or console I/O."
"-Java Concurrency in Practice, page 32."
>}}
* Locks allow only one thread at a time to be active in a section of code.
    * Page 30 has a good illustration of this
* There is a performance overhead to the *synchronized* keyword so locking should be grouped together when possible.
* Threads should spend as little time inside of a synchronization block as possible. This means expensive operations should be placed outside of synchronization blocks to limit the time each thread waits for access.
