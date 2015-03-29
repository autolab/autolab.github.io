---
layout: post
title: "Autolab and Docker"
author: Mihir Pandya

tags: [autograding, docker, tango]
---

# Autolab and Docker #

![]({{site-url}}/assets/docker1.png)

As highlighted in our ['Hello World' post][hello-world], we at Autolab want to make Autograding for all a reality. In pursuit of this vision, we started by making Autolab robust enough to be usable by a variety of classes. Following that, we [open-sourced][gh-autolab] this project after it had been tried and tested at Carnegie Mellon University. While these events have been instrumental in making Autolab usable, the time and effort spent in setting up and configuring Tango, the autograder, is non-trivial. Furthermore, Tango supports a limited number of platforms (Local, EC2) on which it can run jobs. Hence, as part of the latest efforts in making Autograding for all a reality, we are integrating Docker with Autolab in order to significantly reduce the overhead in getting started and to make autograding platform-agnostic. 

This post starts by shedding some light on the concepts Tango uses for autograding. Building on those concepts, this post will then outline how docker is integrated to make autograding more platform-agnostic.

# What it takes to Autograde



[hello-world]: http://autolab.github.io/2015/03/autolab-autograding-for-all/
[gh-autolab]: https://github.com/autolab/Autolab