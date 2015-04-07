---
layout: post
title: "Autolab and Docker"
author: Mihir Pandya

tags: [autograding, docker, tango]
---

![]({{site-url}}/assets/docker.svg)

As highlighted in our ['Hello World' post][hello-world], we at Autolab want to make Autograding for all a reality. In pursuit of this vision, we started by making Autolab robust enough to be usable by a variety of classes. Following that, we [open-sourced][gh-autolab] this project after it had been tried and tested at Carnegie Mellon University. 

While these events have been instrumental in making Autolab usable, the time and effort that needs to be spent in setting up and configuring Tango, the autograder, is non-trivial. Furthermore, Tango supports a limited number of platforms (Local, EC2) on which it can run jobs. Hence, as part of the latest efforts in making Autograding for all a reality, we are [integrating Docker][gh-docker] with Autolab in order to significantly reduce the overhead in getting started and to make autograding platform-agnostic. 

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

At present, the VMM interface has been implemented for any local Linux machine and Amazon EC2. In order to execute a job, Tango's job manager must check if there is an available VM to run the job (`getVMs`), ensure that the VM is reachable (`waitVM`), followed by `copyIn`, `runJob` and `copyOut`.

# Integrating Docker

Docker containers provide process, network and memory isolation. They are also designed to be multi-tenant citizens on any host they run on. A combination of these properties makes docker containers ideal for running autograding jobs. Furthermore, clients have the flexibility of [building any Docker image][docker-img] with customized software and running jobs in those images. Tango's [v1.1.0 release][gh-docker] allows clients to run jobs in docker containers on the same host as Tango, i.e. locally. This option is useful for small classes and needs minimal infrastructure.

In the context of docker, here is what the VMM interface does under the hood:

* `initializeVM` - Nothing to initialize!
* `waitVM` - Nothing to wait for!
* `copyIn` - Create a directory on the host machine and copy all input files to that directory. This directory will be mounted as a volume to the container that is going to run the job.
* `runJob` - Spin up a docker container running the given docker image and mount the previously created volume on to that container. Then, run the job and write the feedback file to that volume.
* `copyOut` - Copy feedback file from the volume to Tango's directory of feedback files. Then, destroy the docker container and its corresponding volume directory.
* `destroyVM` - Destroy the docker container and its corresponding volume directory.
* `getVMs` - Get a list of volume directories that are currently used.

The use of volumes with docker containers has greatly simplified each function! With this simpler approach, getting an end-to-end instance of Autolab with autograding is significantly simpler (refer to the [wiki][gh-docker-wiki] for instructions).

# Next Steps

Integrating Docker with Autolab has paved an exciting future for Autolab and the team as it has made room for greater improvements in Autolab's usability. The immediate thing we are working on is a distributed version of Tango that will run jobs in docker containers across a fleet of user-specified machines accessible over SSH. This feature will allow instructors to dedicate a pool of machines for specific, computationally-intensive assessment for classes like Machine Learning and Distributed Systems.

[hello-world]: http://autolab.github.io/2015/03/autolab-autograding-for-all/
[gh-autolab]: https://github.com/autolab/Autolab
[docker-img]: https://docs.docker.com/userguide/dockerimages/
[gh-docker]: https://github.com/autolab/Tango/releases/tag/v1.1.0
[gh-docker-wiki]: https://github.com/autolab/Tango/wiki/Tango-with-Docker