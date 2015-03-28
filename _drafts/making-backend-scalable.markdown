---
layout: post
title: "Making Autolab's Backend Scalable"
author: Ilter Canberk

tags: [tango, redis, scalability]

---

Tango is the back-end service that receives grading jobs from Autolab's front-end, puts them in a job queue, and assigns them to available containers for grading. Tango came across larger work-loads and huge files as Autolab got more popular this semester.


![Tango's initial Architecture]({{site-url}}/assets/redis1.png)
_Diagram 1: Initial Tango architecture with in-memory job queue_


The existing monolithic model with a single process and an in-memory queue limited the scalability and was prone to crashes that caused the jobs to disappear. Therefore we decided to improve our architecture by switching to multi-process model with a persistent queue.


## Persistent Memory Model using Redis ##

The in-memory job queue was the barrier to making the system robust. We want to store jobs on an independent system and make it *persistent*

![Tango with Persistent Memory Model]({{site-url}}/assets/redis2.png)
_Diagram 2: Tango with Persistent Memory Model_

In this persistent memory model, even if Tango restarts it will keep the same job queue. We could even spin up multiple instances of Tango and they can all work concurrently, sharing the same queue (with a couple problems which I will mention in the next section).

### Choice of Redis ###

We started looking into possible solutions: traditional databases like MySQL, relational databases such as MongoDB and messaging queues such as RabbitMQ, Redis. Looking at the amount of data and the level of nestedness, we decided that we don't need a database with full-range of functionality and robustness. We needed something fast and simple to use. The implementation of our job queue is in the form of a Python dictionary, therefore, a fast key-value store like _Redis_ seemed like the right fit. Setting it up and playing around with it was a breeze, therefore we decided to go with this option.

We are planning to add modules to support the other options in the future. More details about this will be give in the next sub-section.

### Implementation ###

While making the switch, we wanted to keep the ability to run Tango without dependency on the external service in order to keep the backwards competibility and ease of development environment setup.

In order to achieve this we created (pseudo)abstract classes that include the common methods and decide on what is used under the hood based on the configuration.

Since Python doesn't really have abstract classes, we have define methods that would initiate the appropriate class and return it.

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



### Serialization and Marshalling ###

Redis, being a key-value store, only accepts strings as values. Even though the objects we are trying to store are quite simple and flat, we do not want to create a new key-value pair for every attribute of the class. Instead we decided to serialize what we call the 'remote objects' using the `pickle` module included in the standard python library.

A sample method to put the object into the store looks like

```
def set(self, id, obj):
    pickled_obj = pickle.dumps(obj)
    self.r.hset(self.hash_name, str(id), pickled_obj)
    return str(id)   
```

An to get it back you would need:

```	
def get(self, id):
    unpickled_obj = self.r.hget(self.hash_name, str(id))
    obj = pickle.loads(unpickled_obj)
    return obj
```

Pickling seemed like a good solution at first, but when we needed to nest remote objects, e.g. put *TangoMachine*s into the remote *TangoQueue*, we ran into the problem of `self.r`(type of *redis.StrictRedis*) not being serializable. To get around this, we defined custom __getstate__ and __setstate__ methods that would not include self.r attribute in the serialized string, but set it back when the object is deserialized.

```
def __setstate__(self, dict):
    self.__db= getRedisConnection()
    self.__dict__.update(dict)
```

Details of the implementation can be found on [TangoObjects module](https://github.com/autolab/Tango/blob/master/tangoObjects.py).


## Producer Consumer Model ##


So far we have covered how Tango receives grading jobs and stores them. The second most important part of the process is to read from the queue and distribute the jobs to the available containers(cluster of Virtual Machine or Docker). In the initial architecture, the consumer was part of the HTTP server process. A single Tango process would both produce and consume the jobs.

Having a shared queue gave us the ability to run multiple *producers* as explained in the previous section. However, we only needed one *consumer* to distribute the jobs.

Therefore, we decided to separate the consumer from the HTTP server (producer) as a standalone process.

![Tango with Prod/Com Model]({{site-url}}/assets/redis3.png)

_Diagram 3: Tango with Prod/Com processes and Persistent Memory Model_


The advantage of this architecture is that we can launch arbitrary number of HTTP servers as we receive more load. There would be only one consumer process which is responsible for reading from the queue and assigning the jobs. This process gives us a lot of flexibility because it can be run on any node, be stopped and migrated to a different machine at any time.


---

We are currently testing the new architecture and will be posting more about the outcomes as it gets used by more users and we come across interesting cases. 

Please feel free to ask questions using the forum below!
