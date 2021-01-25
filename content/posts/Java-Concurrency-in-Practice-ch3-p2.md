---
title: "Java Concurrency in Practice Ch3 Part 2"
sub_title: "Sharing Objects: publication"
date: 2021-01-24T16:02:48-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/trains_compressed.jpg"
alt_text: "Java Concurrency in Practice Ch3 P2 screenshot"
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
Summary: "Reading and taking notes about 'Java Concurrency in Practice' Chapter 3"
---

Resources:
* [Java Language Specification: Java SE15.](https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html)
* [JavaFx Interop tutorial](https://docs.oracle.com/javase/8/javafx/interoperability-tutorial/concurrency.htm)

{{<tab>}}
    Starting with 3.2 the book begins with talking about how to safely publish and share variables and state as well as providing examples with how state errors can happen. I will be going in a different order than the book because in my head I think it makes more sense.

While it may seem obvious, one of the best ways to avoid any problems with state or shared variables is to simply not share them. The less we share inside of our program, the less state that we have to worry about leaking and causing side effects. One amazingly easy way to handle this is by working with the majority of our variables inside of methods as these require explicit returns or accessing shared variables references in order to share the values.

``` Java
public class MyClass{
    public volatile boolean isReadyFlag;

    // This is shared on the heap memory, but it's safe to share freely because the variable
    // reference is final and the refernce points to an immutable object.
    public static final String MY_NAME = "Mike";

    public static void main(String... args){
        ...// Main stuff here
    }

    public void getReady(){

        if (isReadyFlag){
            // we're checking this value perhaps we exit early or something
            // Remember, this has VOLATILE semantics so we cannot guarantee 
            // this won't change in the middle of us processing
        }

        // This variable is created inside of this thread's stack so it's confined
        // and unless we share it there's no worry about conflict
        ArrayList<String> myNames = getNames();

        // Because of how Java's memory model works with primitives,
        // this value is only copied never shared so it's safe to share 
        // as it'll only create a 'copy'
        int theAnswer = 42;

        /*
            .. Do a lot of processing 
        */


        if (arbitraryCheck){
            // Here we're changing global state so we're no longer isolated
            // Also non-synchronized cannot guarantee the flag is not already 
            // set to true
            this.isReadyFlag = true;
        }
    }

    public void getNames(){
        ArrayList<String> myNames = new ArrayList<>();
        /*
            // Generate all the names
        */
        // Even returning this, we are still within our local context
        // It's only when we share this state where it can be read outside of our 
        // local thread that we've published it to different areas.

        // Think of it like a secret or something put on the internet,
        // once it's done there's no way to guarantee there's no reference to it
        return myNames;
    }

}
```
You can see this listed in the JLS here:
{{<sand-quote
"Memory that can be shared between threads is called shared memory or heap memory. All instance fields, static fields, and array elements are stored in heap memory. In this chapter, we use the term variable to refer to both fields and array elements.Local variables (§14.4), formal method parameters (§8.4.1), and exception handler parameters (§14.20) are never shared between threads and are unaffected by the memory model.Two accesses to (reads of or writes to) the same variable are said to be conflicting if at least one of the accesses is a write."
"https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html#jls-17.4.1"
"JLS Shared Variables"
>}}

* *Local variables (§14.4), formal method parameters (§8.4.1), and exception handler parameters (§14.20) are never shared between threads and are unaffected by the memory model.* - JLS 17.4.1    
    * This is what we did in the prior example, we don't have to worry about our variables if they're not shared.
     
{{<br 3>}}

OK! I know what you're thinking, what good is a variable if I can't share it? It's use is just keeping place of a value while I process some data or perhaps for some logical validation right? Not really, but protecting most of your stateful elements and providing a way for a user of the class to get the requested data you can improve the integrity of the class by ensuring access to mutable state is only done through expected procedures.

Now we're back to the book's 3.2 *Publication and Escape* chapter, it talks about ensuring objects don't simply escape from where they're expected to be. This means that any access to the underlying data structures should be kept to a minimum and if we need to give access to an element of some array or similar structure then we should ensure that we're not leaking out references to the underlying data structure unintentionally.


"The most blatant form of publication is to store a reference in a public static field"
``` Java
public static Set<Secret> knownSecrets;

public void initialize(){
    knownSecrets = HashSet<Secret>();
}
```
Quote and example from the book on pg.40


Here we're just letting anyone access this field, additionally since we're not providing either a synchronized object or any way to ensure a synchronization policy is being observed then any use of this between multiple threads would be considered a broken program. Remember that using unsafe data structures and pieces does not make an unsafe program, it is uncoordinated access between threads to state that causes this.


This leads to the next example on the same page. A program keeps a private field, but then returns a reference to the field when it's asked for some of its data. 

``` Java
class UnsafeStates{
    private String[] states = new String[]{
        "AK", "AL" ...
    };

    public String[] getStates(){
        return states;
    }
}

```
This is a major problem because we've now removed any safety the 'private' keyword afforded us. We have given anyone that requests data a fully mutable copy of our state. Our data is now suspect as it could be changed by other parts of the program, and the thread safety of this object is further compromised because we can no longer guarantee that synchronized access can ever occur. What can we do to avoid copying our state wrong? The answer has many different options, but it also depends upon the underlying data and if it's immutable
* If the underlying data is mutable, then create a copy of the element or of all elements if you want to return a list. return no references to your class's code.
* If the data is immutable, then you can return an immutable copy of the holding data structure as well references to the immutable objects your data owns
* You CANNOT simply return an unmodifiable list to mutable data, as you're still leaving the data in an unprotected state.

{{<br>}}

#### We said that we can return elements if they're immutable, why is that?
An immutable object is always thread safe because it's values do not change. This means that no matter who has the data, they state of the object will always be in a consistent manner. You can even go further and join both an immutable object and a volatile variable to create state from stateless classes.
* I want to be extra clear here, I said 'do not change' not cannot change. There are *effectively immutable* objects and these offer the same kinds of guarantees except you cannot simply trust these values won't change. With proper thread safety techniques, you can build up effective thread safe objects from mutable ones, but you must ensure that when using these objects they're not modified or you'll have additional problems of visibility and atomicity in changes to deal with
* Also I want to point out again, that the thread safety guarantee of _volatile_ is very weak, and is in regards to visibility only. Everyone will be able to see the most current reference object regardless if it's correct or not.

``` Java
public class MyClass{
    // Any change is PUBLISHED AND UPDATES ARE ATOMIC
    // While state cannot change, we can update our state
    // by creating a new ImmutableState object
    public volatile ImmutableState state;

    public MyClass(){
        // update to state that cannot change
        state = ImmutableState.getDefaults();
    }

    public void updateState(int statusCode){
        if (.../* Some logic that might be based on state*/){
            ... // Do stuff
        }
        ... // Do stuff

        // Update state based on conditions
        state = ImmutableState.createNewState(cond1, cond2, ..., condN);
    }

}
```

Here we have immutable state so we cannot have something update us, and then we have a volatile variable so any changes to our state are made atomically! Nice right? Well this is great, but it also comes at the cost of having to create and update through immutable objects as well as the fact that our _volatile_ keyword provides no synchronization except visibility. It's possible that someone could have just changed our state or that we're changing our state two times in a row. We could add another flag indicating 'inChange' or something, but then we'd have to monitor two variables and see their actions in an atomic fashion. Again, as the author of the book said you should consider carefully what kind of thread coordination policy you want 

#### Some systems cannot have shared mutable access, and instead they must rely on coordinated access through other means.
{{<tab>}} One good example is the [JavaFx](https://docs.oracle.com/javase/8/javafx/interoperability-tutorial/concurrency.htm) platform, and pretty much all GUI frameworks, because they use a model of concurrency where only one thread has access to modify data.

{{<sand-quote
"The JavaFX scene graph, which represents the graphical user interface of a JavaFX application, is not thread-safe and can only be accessed and modified from the UI thread also known as the JavaFX Application thread. Implementing long-running tasks on the JavaFX Application thread inevitably makes an application UI unresponsive. A best practice is to do these tasks on one or more background threads and let the JavaFX Application thread process user events."
"https://docs.oracle.com/javase/8/javafx/interoperability-tutorial/concurrency.htm"
"JavaFX: Interoperability"
>}}

{{<br>}}

{{<tab>}}So what does this mean for us? Does this mean it's OK to have this kind of concurrency? Yes, but no. So this concurrency is possible and in some cases is the only thing that is possible; however, it's a fragile system as are depending upon someone NEVER USING THE GUI THREAD and only accessing things through proper channels. It is a problem because you are using constructs inside of a library or platform and not getting any help from the compiler or the language itself to provide any validation or checks that you're doing things properly; all errors must be found the manual way through both automated and hands-on testing. So yes you can use this and it does work, it's often difficult to implement.

{{<tab>}} As a side note, this reminds me of the idea of our current thread bound reactive programming models inside of Java. While we have a lot of power and usability through reactive-streams setting up long chains and using callbacks, I know in Java we have Future's, Promise's, and CompletableFuture's, but it's all the same idea of delegating a task to occur later down the road. Now don't get me wrong, I love syntactical sugar as much as the next person, but even with these more powerful objects to build upon there is the concern about blocking a single thread.

{{<br>}}

#### More than just providing unregulated access to variables, we can improperly construct our class and have the integrity of all of our data be in question since the class hasn't been properly constructed.

{{<sand-quote
"But an object is in a predictable, consistent state only after its constructor returns, so publishing an object from within its constructor can publish an incompletely constructed object. This is true *even if the publication is the last statement in the constructor.* If the 'this' reference escapes during construction, the object is considered *not properly constructed.*"
"Java Concurrency In Practice Pg.41">}}

It's such an important topic that I want to bring up [JLS 17.5](https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html#jls-17.5) that explicitly mentions this: "An object is considered to be completely initialized when its constructor finishes."
* This mentions final fields being in a consistent state only after the constructor has finished, so the JVM is free to wait or provide wrong values for this until the constructor finishes. So you should assume that if you're seeing 
* *"Note that if one constructor invokes another constructor, and the invoked constructor sets a final field, the freeze for the final field takes place at the end of the invoked constructor"* [17.5.1 Semantics of final Fields](https://docs.oracle.com/javase/specs/jls/se15/html/jls-17.html#jls-17.5.1)
* Remember, Java is an interpreted and sometimes compiled language so each JVM works a little differently but all JVMs must adhere to the specification laid out. This means that those designing the JVM will look for optimizations that are allowed within the spec even if they're not what you'd expect. So in this case a JVM may wait until the last possible second to fill out final fields or perhaps it does it early; it's all based upon the JVM being deployed so don't make assumptions.

``` Java 
public class MyClass{

    private final Thread myThread;
    public MyClass(){
        

        myThread = new Thread(){
            @Override
            public void run(){
                System.out.println("Hello Bob");
            }
        };
        // OH NO!!! Doing this will cause the 'this' of the unfinished constructor to escape!!!!
        myThread.start(); // Doesn't matter if it's the last line!!!
    }
}
```

This is a problem, and the book says it's ok to create Threads, Listeners, or other similar types; but you should not run them from the constructor

instead

``` Java
public class MyClass2{
    private final Thread myThread;

    // We make this private so we don't have to worry about someone else calling it
    private MyClass2{
        myThread = new Thread(){...};
        // Don't RUN IT!
    }

    public static void runIt(){
        MyClass2 mc = new MyClass2();
        // Much better, it's fully constructed
        mc.myThread.start();
    }
}
```

So we're at a good place! We have a class that's fully constructed and we're not leaking any state from our own objects.
