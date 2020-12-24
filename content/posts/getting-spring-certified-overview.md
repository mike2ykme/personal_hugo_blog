---
title: "Getting Spring Certified"
sub_title: "Getting Started"
date: 2020-12-23T20:49:04-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/spring.jpg"
alt_text: "Getting Spring Certified Overview"
year: "2020"
month: "2020/12"
draft: false
categories:
  - Career
tags:
  - certification
  - Java
  - Spring
series:
---

So I've finally decided that I'm going to get Pivotal's __Spring Certified Professional__ and there's so many and so few resources available at the same time!

<!--more-->

In the past, when I was reviewing certification material,there always seemed to be specific books aimed at helping you pass the certification that were suggested by the course itself or were highly recommended by the community, and with the Spring certification I've found this to not be the case. Pivotal recommends that you take the multi-day course where you are led by an instructor who will review all course material expected to be on the exam. If you have both the money and the time then this could be a very good option as you'll be receiving instruction directly from the team that built out the product.

Regardless of the option you're going to choose, I've chosen to go the *self taught* route; which means that I'm going to scrounge the internet and look at study guides and hope that I know enough to pass!

Also, this is a test on the Spring framework and not simply the *boot* section, boot is included so you'll need to understand it as well, so you must make sure that you're reading material covering the actual framework configuration and not something that depends upon a lot of auto-configuration to get you up and running. This may seem like a small matter, but if you're like me and tried to find resources on YouTube and Pluralsight you're going to find a lot of the information available refers to *Spring Boot* and this is not idea. On one hand this is just a lot of configuration that's being automated away so you can better understand the core concepts; yet after reviewing a lot of the material you're going to find a lot of "HOW!" and "WHAT!" moments where things will work in boot differently and only upon further inspection you'll see that Spring's team has gone ahead and given you the best default configuration that you should use. Now I'm not complaining here! This is a great feature, and if you're setting up a DB connection to a production database you really should have a connection pool setup from the beginning, but when you're trying to figure out what's happening it's a lot of indirection to review.

So what am I going to study and use for resources? I'm glad you asked because it's kind of a weird situation right now. Spring has updated their framework to 5.x and we've got quite a few under the hood improvements and changes, but the general API has stayed the same in a lot of areas as well. This is great news for people who've worked with Spring for a long time because it means that you'll still be able to use a lot of the information that you've been using. There's still support for XML annotation, but the cert doesn't cover it so you'll need to be ready for the new Java configuration. This means that I've actually found a lot of good content in some older material, especially the Spring in Action 4th edition book (There's the 5th edition but it's really about Spring Boot and won't help you as much, just get the older version as it's way more useful to find out what is needed in a general sense).


OK enough rambling about the exam here's a list of what I'm using to review and prepare

Starting with the official resources:

1. [Spring Professional Study guide](https://pivotalcontent.s3.amazonaws.com/academy/Spring-Professional-Certification-Study-Guide.pdf)

2. [Spring Professional Exam Brief](https://pivotalcontent.s3.amazonaws.com/training/exam-briefs/Pivotal_ExamBrief_SpringProfessional.pdf)
3. Ivan Kriszan's excellent Spring Certification review book [Core Spring 5 Certification in Detail](https://www.kobo.com/en/en/ebook/core-spring-5-certification-in-detail)
    * Seriously this is a good book that has a lot of the answers for each of the review sections.
4. [Spring in Action 4th Edition](https://www.amazon.com/Spring-Action-Covers-4/dp/161729120X/ref=sr_1_3?dchild=1&keywords=spring+in+action+4th&qid=1608781824&sr=8-3)
    * This has a lot of great material to review about setting up and using the features
5. [Pivotal Certified Professional Core Spring 5 Developer Exam: A Study Guide Using Spring Framework 5 2nd ed. Edition](https://www.amazon.com/Pivotal-Certified-Professional-Spring-Developer/dp/1484251350/ref=sr_1_2?dchild=1&keywords=Spring+Professional&qid=1608778624&sr=8-2)
    * This is really the only "STUDY GUIDE" book that I could find for the exam and I've found the content to be pretty good.

___
___

Ideally you would be able to get all of these resources provided to you by your workplace or through a subscription service, except for Ivan's excellent resource I think this is only available through Kobo now, I've found most of the resources through O'Reilly's subscription service. I've attached the Amazon links but I'm not an affiliate so please feel free to buy these in person or online somewhere else. Hey that's 4th edition book is pretty old by now so you can probably snag one up on the cheap from ebay or somewhere else!