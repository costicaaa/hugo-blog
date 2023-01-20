---
title: "Continuous Delivery: HTML to Kubernetes - the why"
date: 2023-01-08T22:39:42+02:00
draft: false
---

I think Continuous Delivery is the magic sauce that allows `web` to be the go-to platform for all software nowadays.

Web development is about evolving software: from idea to actual requirements, from an MVP tested by your friends & family to a system used by millions of users.

# Why

The internet is full of articles on "How to create a CRUD API with Node.js", "How to configure Nginx as a reverse proxy" or "How to do X for Y".

However, I think there's a lack of details about _why_ some patterns and ideologies emerged in the last few years.

What I think is missing (or maybe is only accessible via paid courses) from the general "internet archive" is how parts of a system work together in the _whole lifecycle of a project_, and how a CI/CD pipeline serves so many uses and it allows you to deliver fast, bit-sized iterations of your software so that _users can benefit from it_ as soon as you are done coding it.

That's not an easy task.

There are a lot of trade-offs that should be considered for the long-term success of a project: from small decisions like what library to use to validate the input to architectural decisions about how to make a system elastic but cost-efficient at the same time, all the way to being able to iterate fast and don't pay 300 devs when 30 could suffice.

# How I see things

`Web dev` is not about Javascript or Kubernetes - not only. And it certainly is not about "PHP is dying" vs "use Node!".

It is about **delivering software**. It has evolved and nowadays it also means delivering [all kinds of software](https://costica.dev/posts/web-apps-my-mental-cheat-sheet/) in a browser.

My hot-takes about software engineering, with a naturally _biased_ mentality coming from web development:

* Translating the product requirements into technical ones is not enough; one should take into account actual _deliverable milestones_.
* It is better to throw away 2 months of work that was tested by real users than spend 6 months to "do it right".
* If you know how to test a feature or project, the actual coding is a breeze.
* Dev experience has a huge impact on `time-to-market`. Devs should have an easy way **to code** and **to prove** that it works.

Yes, I'm the kind of person that wants to get the software out the door as soon as possible. I'm sold on the idea that software not delivered is worth **0$**.

Yes, this translates into the `iterative` / `agile` approach.

I also think that moving too fast and delivering bad software is just as bad.

# The problem

So... how? How can one move faster, and get actual software out the door (in users' browsers) when `web dev` nowadays means frontend*
* Backend
* CDNs
* Cloud
* 3000 javascript libraries
* Microservices
* Kubernetes
* S3 (or S3-compatible)
* Serverless
* Kafka
* Go
* Rust
* Java
* Async processing
* MapReduce
* Nginx
* Haproxy
* L4 LB
* L7 LB
* Git
* DevOps
* etc

The list can go on. The list does go on.


# My contribution

**The bad news**: if you want to create an environment where `web dev` is **actually productive** you need each web developer to be an entire IT department.

Yes, it used to be a joke running on LinkedIn a couple of years ago.

IMO, that joke has, unfortunately, become true: it doesn't matter if you want to work as a frontend, backend, full-stack, DevOps, cloud engineer, etc.

As a "web developer", you now need to understand the big picture of what `delivering software in a browser` means.

**The good news**: I don't think it is impossible.

You don't have to be a master in all areas in order to navigate through what `web` means.

Quite the opposite - having a general idea about how things work together is far more important than mastering **only** one topic.

That's why I am writing down my mental model - my cheatsheet, if you will. This is what this whole "Continuous Delivery: HTML to Kubernetes" series is about.

# How

Let this be the start of a "how to web in modern ages" series. My rough idea of what's important and how to tackle it:

First, set up the basics of how the internet works:
* [Delivering software in a browser - Frontend apps](https://costica.dev/posts/web-apps-my-mental-cheat-sheet/)
* The strong woman behind the frontend - Web servers

The next step is to see how one can integrate them and how to test them. How to be efficient when coding. How to _deliver_ fast.

After we have a working PoC, we are going to scale it up using a local Kubernetes cluster and deep dive into what delivering software at scale means, while still being able to be efficient when coding.

# Necessary disclaimer
It is a biased tutorial of what should you focus on if you want to deliver good, scalable software, in a timely manner.

That means there will be a lot of focus on the CI/CD, testing, and understanding **how** & **why** things work in the modern world.

# Closing notes

Fair warning: most likely, the plan will change while I write different articles and realize I want to write about things in a different order - or about other things altogether.

And that's ok because that's what `web dev` is about: delivering software **before** requirements or priorities change.

I would also like to get input from the community. I am open to changing my plan (see what I did there?) if I find out certain topics or areas would be of greater interest than my plan.  So don't be shy, say `Hi` 👋...

Ok now, go on, we can't go into Kubernetes if we can't display anything in a browser. Go get familiar with [web apps](https://costica.dev/posts/web-apps-my-mental-cheat-sheet/), because
understanding the front-end is the stepping stone to understanding how everything works. And that statement comes from a backend dev, btw ;)

By-bye! 

