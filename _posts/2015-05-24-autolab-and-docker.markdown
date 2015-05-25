---
layout: post
title: "Autolab and Docker"
date: 2015-05-24T22:40:07-07:00
author: Mihir Pandya

tags: [autograding, docker, tango]
---

![]({{site.baseurl}}/assets/docker.svg)

Autolab started in 2010 and had a very humble beginning. It was used for only one class with ~300 students and simply ran on one laptop! Following that, its merits were realized by other courses at Carnegie Mellon resulting in nothing short of a *success catastrophe*: ~3000 students use it every semester, 77 courses have used Autolab as of Fall 2014 and autograding jobs are run on five Dell R410 machines with 2x Intel E5520 CPUS and 8 Nehalem cores.

While it's really fun to mess around with these sweet, kickass machines, we realized we have to snap back to reality and think about a better way to run autograding jobs. This post outlines some basic autograding concepts, some problems we faced with the existing model and finally describes how Docker is integrated and how we hope it solves all our problems!

# What it takes to Autograde

__Terminology:__ Tango is the autograder used by Autolab.

A guarantee that Tango must provide is that the environment in which a job runs remains homogeneous between jobs. Hence, Tango executes these jobs on virtual machines that are booted with user-specified images. In order to run jobs on a particular platform such as Amazon EC2, OpenStack or even RackSpace, the Virtual Machine Management (VMM) interface must be implemented for that platform. The lifecycle of a job is governed by the [VMM interface][gh-sample-code] outlined below:

![]({{site.baseurl}}/assets/autograde.svg)

* `initializeVM` - Initializes a VM for that platform.

* `waitVM` - Wait for a VM to be accessible

* `copyIn` - Copy all necessary input files to run the job.

* `runJob` - Invoke the autodriver to run the job. The autodriver instruments the job, runs it in a controlled environment and restores the system state after job completion.

* `copyOut` - Copy feedback file from the VM.

* `destroyVM` - Destroy a given VM.

* `getVMs` - Get a list of VMs in each VM pool.

At present, the VMM interface has been implemented for any local Linux machine and Amazon EC2. In order to execute a job, Tango's job manager must check if there is an available VM to run the job (`getVMs`), ensure that the VM is reachable (`waitVM`), followed by performing the `copyIn`, `runJob` and `copyOut` operations.

# Integrating Docker

Docker containers provide process, network and memory isolation. They are also designed to be multi-tenant citizens on any host they run on. A combination of these properties makes Docker containers ideal for running autograding jobs. Furthermore, clients have the flexibility of [building and running jobs in any Docker image][docker-img] with customized software.

In the context of Docker, here is what the VMM interface does under the hood:

* `initializeVM`: Assign a host machine for container to run on. Use round-robin for assignment.

* `waitVM`: Ensure host machine is reachable by SSH.

* `copyIn`: Create a directory on host machine and copy all input files to that directory.

* `runJob`: Start a Docker container with the given Docker image and mount the previously created directory as a volume on to that container. Then, run the job and write the feedback file to that volume.
* `copyOut`: Copy feedback file from the volume to Tango’s directory of feedback files.

* `destroyVM`: Destroy the Docker container (docker rm CONTAINER) and its corresponding volume directory on this host machine.

* `getVMs` - Get a list of volume directories that are currently used.

Clearly, implementing the VMM interface using Docker containers has greatly simplified each function! With this simpler approach, getting an end-to-end instance of Autolab with autograding is significantly simpler. Be sure to check out the [wiki][gh-docker-wiki] for specific setup instructions.

# Next Steps

Implementing the Docker VMMS was the easy part. What keeps most engineers awake at night is of course testing. While we have tested this implementation by running specific labs hundreds of times simultaneously, we know that is not enough. We are currently working on writing A/B tests that will run an incoming job on both Docker and Tashi. We hope this will give us some clarity on the correctness of this approach. Maybe then, we can think about performance testing. 

In the meantime, we'd love to hear any feedback, suggestions, criticisms, adulations or rants about what we have done so far and what you think would help us next.

Thanks for reading!

[hello-world]: http://autolab.github.io/2015/03/autolab-autograding-for-all/
[gh-autolab]: https://github.com/autolab/Autolab
[docker-img]: https://docs.docker.com/userguide/dockerimages/
[gh-docker]: https://github.com/autolab/Tango/releases/tag/v1.1.0
[gh-docker-wiki]: https://github.com/autolab/Tango/wiki/Tango-with-Docker
[gh-sample-code]: https://github.com/autolab/Tango/blob/master/vmms/localDocker.py
[gh-tashi]: https://github.com/apache/tashi