---
title: "Hosting on Nearlyfreespeech.net"
sub_title: ""
date: 2021-03-02T20:12:10-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/board-2440249_640.jpg"
alt_text: "Switching to Nearlyfreespeech screenshot"
year: "2021"
month: "2021/03"
draft: false
categories:
- Personal
tags:
- Hosting
- Programming
- Go
- Java
series:
Summary: Using Nearlyfreespeech.net for this site
---

My intentions with doing anything on this site are more to encourage myself to keep on working on developing my skills. Software development is truly a craft where someone needs to take time and both enjoy the process as well as work to improve themselves; yet unlike most hobbies, software lets you create and share what you do in a way that anyone can really enjoy or benefit from. Much like wood and stone, software is a blank canvas that allows you to create something that can be used in a strictly functional manner, or something that can be enjoyed as well as be functional. Regardless, the reason that software is appealing to a lot of people is because of the ability to take what's in your head and let it out into the world. 

So what does all that have to do with setting up a personal site? The idea of craftsmanship has everything to do with it. I'm not a web developer by trade, but because the idea is fascinating I'm building everything here myself using whatever resources I can find available. So with this thought in mind, I decided that I'd like to be able to build something that I can use to record and better understand my learning, learning in the open, as well as give myself a regular project to work on. This site is so small, but even with that it has taken quite a bit of time to get things in a basic working order and I'm not happy with a lot of the design choices I made. This also leads me to my hosting choice, I was previously just going to use something like github pages to host everything, but have since decided that along with the ability to create a static site I'd like to be able to have some back-end functionality if I'd like it. So I've moved everything to [Nearlyfreespeech.net](https://Nearlyfreespeech.net). Why not go with a large platform like AWS, Azure or GCP? Well a couple years ago I discovered that I'm a fan of the underdog, so when I was thinking about choosing a hosting provider I never really considered them. They're all good for what they're doing, but they're full of extra complexity that I have **zero** interest in dealing with for my site. Having to deal with route 52 to just setup routing is no fun, and if I need something with a more dedicated VPS or powerful shared hosting service I'll probably go with either Linode or rimuhosting as both of these provide some features that I like. Also, there's the point that dedicated services can cost a LOT of money or require you to sign up for long terms of service in order to qualify for a good pricing. I'm not really a fan of that model, I wanted to do something that would let me do what I wanted as well as not require either a long term commitment or any kind of large monthly bill. 

This brings me to the actual exciting part of this post! NSF.net basically gives you a couple options to decide what kind of hosting that you'd like to use along with a $0.02 credit. Yeah, 2 cents to play around with their service. Now, that may seem like a ridiculously small amount, but to be fair I did some research and with that amount you could probably run some static websites for a day or 2 with that amount see **[nearlyfreespeech.net pricing](https://www.nearlyfreespeech.net/services/pricing)**. Yeah, I know I'm cheap and you can make all the commitment jokes you want but I'm really intrigued by a hosting service that will let me run my own web server on their backend and just point an apache server to my stuff in order to show my content. Yeah, that point was actually one of the *perks* I was excited about. I've been able to try hosting some static content through both a Spring boot app as well as a go backend with some pretty wildly different results.

So before we go any further, I need to mention that I like Java and it was really the first programming language that I used well; I also have a Spring certification and chose the bare minimum setup for a Spring Boot app, but even in looking at a 5 minute breakdown of usage it becomes clear that memory usage of Java is huge!!! 

{{<br>}}

Let's start off with an image of what all the services looked like together:
{{<w3-img "/nfs/all-graph.jpg" "all-graph" >}}
This content has 3 data points, where the first and second are Spring Boot apps. They were running one *test* endpoint as well as serving static content from the public folder. The last was GoFiber doing the same thing, all were using templates to generate the one *test* endpoint they each had. Also the large time discrepancy of a week was because of work life balance. I had to have a week off so I could pay the bills :) . The time difference between the last two endpoints was because I wanted to review I was doing things correctly in Spring before making another test.


 Here I knew what I wanted to do so I setup a Spring boot app to just verify I could setup a service. It only had one endpoint that would generate a thymeleaf template based page that showed a joke as well as provide static access to html files:
<!-- {{<w3-img "/nfs/custom-server.jpg" "custom-server" >}} -->

{{<br>}}

Let's look at Java's performance in more detail first:
{{<w3-img "/nfs/java-performance.jpg" "java-performance" >}}
We have the expected spike of CPU usage to load up all the objects and models into memory, but what was kind of unexpected was the delay in memory usage for about 10 minutes there wasn't a huge spike in RAM usage, but after we loaded everything we stayed around 330 MiB of memory dropping to around 290 before I started to shutdown the system. 

Does this mean that Java on top of the JVM isn't great for small services or for smaller sites?
* Yes, this is a clear sign that Java isn't a great fit for this kind of operation. I can clearly see that running for 1 hour 15 minutes that memory usage never really dropped even with a 30 minute break in the middle where I wasn't performing any kind of operations just monitoring performance. Now does this mean that Java can't work better than other language when it has headroom to grow and resources aren't a constraint? This one I'm not so sure about because even now, we're running the JVM in an *optimized* state because it's able to break apart some of it's String characters and references from memory into smaller versions because we're running on a JVM with lower than 32 GB of memory.

{{<br>}}

Regardless, I really wasn't expecting to find Go have such a drastic difference:
{{<w3-img "/nfs/go-performance.jpg" "go-performance" >}}
You can see that we've gone from 333 MiB to 11, and after running for **days** it has been running around 16. Now you can say this is a reflection of my site being such low traffic, and I will completely agree that the traffic coming here is such a low amount that we're basically monitoring the idling performance of both of the frameworks instead of true load capacity. Yet, when reviewing this statement, I'm not sure it even bears reality as most sites aren't going to run under the load of a large platform such as YouTube or Facebook. How many of us are working for organizations let alone our personal sites that generate so much traffic that we have to develop new tools and techniques in order to keep up with performance demands?

While this hasn't turned me away from Java or Spring, I can see that in resource constrained environments like a shared hosting provider I will probably consider using a different tool.


<!-- {{<w3-img "/nfs/setup-alias.jpg" "setup-alias" >}}
{{<w3-img "/nfs/setup-daemon.jpg" "setup-daemon" >}}
{{<w3-img "/nfs/setup-proxy.jpg" "setup-proxy" >}} -->

