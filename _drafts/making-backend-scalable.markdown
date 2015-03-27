---
layout: post
title: "Making Autolab's Backend Scalable"
author: Ilter Canberk

tags: [autolab, community, backend, scalability]
---

# Making Autolab's Backend Scalable #

Tango is the back-end service that receives grading jobs from Autolab's front-end, puts them in a job queue and assign these jobs to the available containers that are responsible of running the grading scripts. 


![Tango's initial Architecture]({{site-url}}/assets/redis1.png)


As Autolab gained more adoption, we started to come across larger work-loads and autograders containing huge files. The existing monolithic model was not giving us the necessary flexibility to deal with these cases and was prone to crashes that caused system-wise restarts. Therefore we decided to improve our architecture to not rely on an in memory queue and contain different processes for putting and getting jobs from the queue.


## Persistent Memory Model using Redis

Job queue being stored in-memory was the biggest block in front of making the system robust. We want to store this data on an independent system and make it *persistent*

![Tango's initial Architecture]({{site-url}}/assets/redis2.png)

In this persistent memory model, even if Tango  dies and restarts again it will keep the same job queue. We could even spin up multiple instances of Tango and they can all work at the same time, using the same queue (with couple problems which I will mention in the next section).

### Choice of Redis

We started looking into possible solutions: traditional databases like MySQL, relational databases such as MongoDB and messaging queues such as RabbitMQ, Redis. Looking at the amount of data and the level of nestedness, we decided that we don't need a database with full-range of functionality and robustness. We needed something fast and simple to use. The implementation of our job queue was actually in the form of a Python dictionary, therefore, a fast key-value store like _Redis_ seemed like the right fit. Setting it up and playing around with it was a breeze, therefore we decided to go with this option.

We are planning to add modules to support the other options in the future. More details about this will be give in the next sub-section.

### Implementation

While making this big switch, we still wanted to keep the ability to run Tango without dependency on the external service in order to keep the backwards competibility and ease of development setup.

In order to achieve this we created (pseudo)abstract classes that include the common methods and decide on what is used under the hood based on the configuration.

Since Python doesn't really have abstract classes, we have defined methods that would initiate the appropriate class and return it.

```
# This is an abstract class that decides on 
# if we should initiate a TangoRemoteDictionary or TangoNativeDictionary
# Since there are no abstract classes in Python, we use a simple method
def TangoDictionary(object_name):
    if Config.USE_REDIS:
        return TangoRemoteDictionary(object_name)
    else:
        return TangoNativeDictionary()
```
[See the full code here.](https://github.com/autolab/Tango/blob/master/tangoObjects.py#L232)

In the future, we can have other implementations, such as MongoDB, and just add it as an option here. For example: 

```
def TangoDictionary(object_name):
    if Config.USE_REDIS:
        return TangoRemoteDictionary(object_name)
    elif Config.USE_MONGO:
        return TangoMongoDictionary(object_name)
    else:
        return TangoNativeDictionary()
```



### Marshalling

## Producer Consumer Model
