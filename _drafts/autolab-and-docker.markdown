---
layout: post
title: "Autolab and Docker"
author: Mihir Pandya

tags: [autograding, docker, tango]
---

![]({{site-url}}/images/docker1.png)

As highlighted in our ['Hello World' post][hello-world], we at Autolab want to make Autograding for all a reality. In pursuit of this vision, we started by making Autolab robust enough to be usable by a variety of classes. Following that, we [open-sourced][gh-autolab] this project after it had been tried and tested at Carnegie Mellon University. 

While these events have been instrumental in making Autolab usable, the time and effort spent in setting up and configuring Tango, the autograder, is non-trivial. Furthermore, Tango supports a limited number of platforms (Local, EC2) on which it can run jobs. Hence, as part of the latest efforts in making Autograding for all a reality, we are integrating Docker with Autolab in order to significantly reduce the overhead in getting started and to make autograding platform-agnostic. 

This post starts by shedding some light on the concepts Tango uses for autograding. Building on those concepts, this post will then outline how docker is integrated with Tango.

# What it takes to Autograde

A guarantee that Tango must provide is that the environment in which a job runs must remain homogeneous between jobs. Hence, Tango executes these jobs on Virtual Machines that are booted with user-specified images. In order to run jobs on a particular platform such as Amazon EC2, OpenStack or even RackSpace, the VMM interface must be implemented for that platform. The VMM interface is as follows:

* `initializeVM` - Initializes a VM for that platform.
* `waitVM` - Wait for a VM to be accessible
* `copyIn` - Copy all necessary input files to run the job.
* `runJob` - Invoke the autodriver to run the job. The autodriver instruments the job, runs it in a controlled environment and restores the system state after job completion.
* `copyOut` - Copy feedback file from the VM.
* `destroyVM` - Destroy a given VM.
* `getVMs` - Get a list of VMs in each VM pool.

At present, the VMM interface has been implemented for a local Linux machine and Amazon EC2. In order to execute a job, Tango's job manager must check if there is an available VM to run the job (`getVMs`), ensure that the VM is reachable (`waitVM`), followed by `copyIn`, `runJob` and `copyOut`.

# Integrating Docker



[hello-world]: http://autolab.github.io/2015/03/autolab-autograding-for-all/
[gh-autolab]: https://github.com/autolab/Autolab