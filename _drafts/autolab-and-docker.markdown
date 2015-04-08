---
layout: post
title: "Autolab and Docker"
author: Mihir Pandya

tags: [autograding, docker, tango]
---

![]({{site.baseurl}}/assets/docker.svg)

As highlighted in our ['Hello World' post][hello-world], we at Autolab want to make Autograding for All a reality. In pursuit of this vision, we started by making Autolab robust enough for a variety of classes. Following that, we [open-sourced][gh-autolab] this project after it had been tried and tested at Carnegie Mellon University. 

While these events have been instrumental in making Autolab usable, the time and effort that needs to be spent in setting up and configuring Tango, the autograding backend, is non-trivial. Furthermore, Tango needs a platform on which it can run jobs. At present, it supports a distributed CMU-specific platform (called Tashi). It also allows jobs to run locally on a single server or laptop, or even a cluster of EC2 machines. However, moving forward we wanted to provide more options that simplified the process of developing, testing, and deploying Tango. Hence, as part of the latest efforts in making Autograding for All a reality, we are [integrating Docker][gh-docker] with Autolab in order to significantly reduce the overhead in getting started and making autograding platform-agnostic.

This post starts by shedding some light on the concepts Tango uses for autograding. Building on those concepts, this post will then outline how Docker is integrated with Tango.

# What it takes to Autograde

A guarantee that Tango must provide is that the environment in which a job runs remains homogeneous between jobs. Hence, Tango executes these jobs on virtual machines that are booted with user-specified images. In order to run jobs on a particular platform such as Amazon EC2, OpenStack or even RackSpace, the Virtual Machine Management (VMM) interface must be implemented for that platform. The [VMM interface][gh-sample-code] is as follows:

* `initializeVM` - Initializes a VM for that platform.
* `waitVM` - Wait for a VM to be accessible
* `copyIn` - Copy all necessary input files to run the job.
* `runJob` - Invoke the autodriver to run the job. The autodriver instruments the job, runs it in a controlled environment and restores the system state after job completion.
* `copyOut` - Copy feedback file from the VM.
* `destroyVM` - Destroy a given VM.
* `getVMs` - Get a list of VMs in each VM pool.

At present, the VMM interface has been implemented for any local Linux machine and Amazon EC2. In order to execute a job, Tango's job manager must check if there is an available VM to run the job (`getVMs`), ensure that the VM is reachable (`waitVM`), followed by performing the `copyIn`, `runJob` and `copyOut` operations.

# Integrating Docker

Docker containers provide process, network and memory isolation. They are also designed to be multi-tenant citizens on any host they run on. A combination of these properties makes Docker containers ideal for running autograding jobs. Furthermore, clients have the flexibility of [building any Docker image][docker-img] with customized software and running jobs in those images. Tango's [v1.1.0 release][gh-docker] allows clients to run jobs in Docker containers on the same host as Tango, i.e. locally. This option is useful for small classes and needs minimal infrastructure.

In the context of Docker, here is what the VMM interface does under the hood:

* `initializeVM` - Nothing to initialize since Docker containers are designed to be ephemeral.
* `waitVM` - Nothing to wait for since Docker containers are designed to be on-demand.
* `copyIn` - Create a directory on the host machine and copy all input files to that directory. This directory is then mounted as a volume to the container that is going to run the job.
* `runJob` - Spin up a Docker container running the given Docker image and mount the previously created volume on to that container. Then, run the job and write the feedback file to that volume.
* `copyOut` - Copy feedback file from the volume to Tango's directory of feedback files. Then, perform `destroyVM` in order to clean up after the job.
* `destroyVM` - Destroy the Docker container (`docker rm CONTAINER`) and its corresponding volume directory.
* `getVMs` - Get a list of volume directories that are currently used.

Clearly, implementing the VMM interface using Docker containers has greatly simplified each function! With this simpler approach, getting an end-to-end instance of Autolab with autograding is significantly simpler. Be sure to check out the [wiki][gh-docker-wiki] for specific setup instructions.

# Next Steps

Integrating Docker with Autolab has paved an exciting future for Autolab and the team as it has made room for greater improvements in Autolab's usability. The logical next step we are working on is a *distributed* version of Tango that will run jobs in Docker containers across a fleet of user-specified machines accessible over SSH. This feature will allow administrators to dedicate a pool of machines for specific, computationally-intensive assessment for assessments like Machine Learning and Distributed Systems, making it easier for administrators to manage and scale an entire department's worth of unique needs. With this in mind, feel free to leave any comments or questions below or shoot the devs an email at autolab-dev@andrew.cmu.edu to learn more about what's brewing under the hood :)

[hello-world]: http://autolab.github.io/2015/03/autolab-autograding-for-all/
[gh-autolab]: https://github.com/autolab/Autolab
[docker-img]: https://docs.docker.com/userguide/dockerimages/
[gh-docker]: https://github.com/autolab/Tango/releases/tag/v1.1.0
[gh-docker-wiki]: https://github.com/autolab/Tango/wiki/Tango-with-Docker
[gh-sample-code]: https://github.com/autolab/Tango/blob/master/vmms/localDocker.py