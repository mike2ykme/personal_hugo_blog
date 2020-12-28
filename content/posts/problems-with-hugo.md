---
title: "Learning to blog with Hugo"
sub_title: "General experience with Hugo"
date: 2020-12-27T22:57:47-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/adult_with_laptop_compressed.jpg"
alt_text: "Problems With Hugo screenshot"
year: "2020"
month: "2020/12"
draft: true
categories:
- Personal
tags:
- Go
- Hugo
- blogging
series:
Summary: "Learning to blog with Hugo"
---

{{<tab>}} I'm think I'm a reluctant blogger; I know people who love to post on social media or to post about things they're learning or doing on a daily basis, but I just don't think I'm that interesting or great at anything. Yet, I find myself stuck in a position where I need to continue to learn new things and find new ways to make myself accountable and have been told to __learn in the open__ through blogging about what I'm reading/doing. It's taught me a couple of things right from the start:
* I'm not great with css or with HTML but I can kind of fumble around with it to get *most* of what I want.
* You're never going to be ready to do everything so just do it!
    * I'm not fully confident on my web dev skills, and you can see from my strikingly simple and minimalist design that I don't want to over reach my skills.
* You should try and do something different and learn new skills wherever and whenever you can.
    * I want to build out a website to share what I'm learning, but I'm using new technologies so that I can be better prepared for future challenges.


Anyways, I've mostly enjoyed working with Hugo but there are some definite downsides to using it. 
1. The documentation can be confusing at times.
2. The way pages are generated (single,list,index, _default) can be confusing
3. Templating isn't obvious, and you have to work within the limitations of the system.
    * Now this can be a good thing, because the limits help you to create nice solutions that you might not have thought of before.
4. Static sites can do so much more that I thought they could. Hugo has built in integration with Google, Disqus, Twitter, and search tools. You can build search right into your site using a static json file that you can generate statically and then push online.
    * I've been a huge fan of JS frameworks, especially Svelte, and have thought about building out something like this for a while but the load times have always given me pause. Why would I want to spend time building out a site that would basically take markdown and generate content. JS is a great tool, but does every website need to have it? What purpose does a blog with JS really serve?
5. Markdown kinda sucks! I know it's meant to just get some content onto a page, but I spend a lot of my time working out how to make things look nice!
6. I have literally updated my static site and had everything removed.
    * This is kind of my fault. I'm trying to script out the basic steps that I do every time I generate an update because I'd rather just push a button and it deploy. Anyways, yeah... I'm not sure what happened but it uploaded nothing and every file was removed!!! 


Generally I'd say I recommend using a static site generator like Hugo.
There were lots of problems to get started, one I'm trying to use with under WSL2 on a windows laptop so that took a little longer to setup, but that's kind of my own fault for taking a round about path, but I can generally say that I prefer working on a *Nix terminal more than a windows cmd line so the tradeoff in startup time was worth it.
Additionally, I would not have been able to understand nearly as much as I do if it hadn't been for doing some small amount of research and lucking out and find a recent book and a pretty decent playlist on youtube to get started.
So I'm not sure if it is just me having a problem getting started and really understanding the *idea* behind Hugo's page types but I don't think it was super intuitive and I would have definitely struggled for a lot longer if I just tried to use the documentation.
* [This YouTube playlist](https://youtube.com/playlist?list=PLLAZ4kZ9dFpOnyRlyS-liKL5ReHDcj4G3) is a pretty good starting point.
* And this book: [Build Websites with Hugo: Fast Web Development with Markdown ](https://www.amazon.com/Build-Websites-Hugo-Development-Markdown/dp/1680507265)
    * I don't think this book would be something I'd keep as reference material. It was more like an extensive tutorial on using Hugo's features and a couple quick recipes on how to do some of the basic things. All in all, you can get by with some of the basics on the Hugo website for how-to information but once you get to doing things you'll find you need to look up answers on the forums and other resources.


I've also found out that pictures are important! Here's 3 sites that offer free images that you can use:
1. www.pexels.com
2. www.pixabay.com
3. www.unsplash.com

