---
layout: post
title: "Autolab API: Opening Up Autolab"
date: 2018-04-19T14:00:00-04:00
author: 'Billy Zhu'
tags: ['release','api']
comments: true
---

It's almost midnight and you're working over ssh on that programming assignment that's about to hit its deadline. You hit compile and run your local test cases. Everything passes, phew, but unfortunately you can't stop there. You still need to scp the handin file back to your local computer and upload the tarball onto Autolab before midnight (aka 11:59PM).

---

One of the core goals of Autolab is to make teaching easier, and user experience is central to this goal. Scenarios like the one above is proof that the current web interface isn't always convenient. The visual overhaul we introduced with Autolab 2.0 last year was one step on our journey towards a better user experience. Today, we're excited to invite everyone to join us on the next step of this journey with the release of the Autolab API, a HTTP interface that allows anyone to develop clients that interact with Autolab programatically. With this API, we're opening up many common features of Autolab beyond our traditional web interface. Developers will be able to create apps and services that integrate with Autolab to provide more versatile experiences for students and instructors.

In this post, I'll walk through how the API works for users of Autolab, and then go through autolab-cli, our first-party command line client. For interested developers, we also recommend checking out our full [API documentation][api-docs].

### Terminology

- user: a user of autolab (student/instructor)
- client: a program that uses the autolab api
- developer: a person that develops clients

# What should I know as a user?

The web interface that has served us well for many years is no longer the only way to use Autolab. By creating this API, we're inviting the community to join us in making Autolab more versatile and convenient: Whether it be with a mobile app, a command line tool, a browser extension, or something we've never even thought of.

What this means for users is that you can now use third-party programs to access Autolab to view assignments, download handouts, and even submit your solutions. For now, all developers will be manually vetted by our team to ensure quality of the clients. However, it is still important that users understand how clients interact with the system.

## Granting access

As a user of Autolab, when you want to use a client for the first time, you need to grant access to the client so that it can interact with Autolab for you.

- For clients that have access to a web browser (e.g. mobile apps, web apps), they will **redirect** you directly to the Grant Permissions page on Autolab.
- For clients that _don't_ have access to a web browser (e.g. command line programs), the client will present to the user a **6-digit code** (case sensitive) that should be entered on the Autolab website.

_Note_: Third-party clients never ask for your username or password on Autolab. Never enter them anywhere else except on the Autolab website (always check the page url before entering your credentials).

![API Manual Activation Page]({{ site-url }}/assets/api/api-activate.png)
_Manual activation page_

When you enter the code on the website and click "Activate", you will be taken to the Grant Permissions page.

![API Grant Permissions Page]({{ site-url }}/assets/api/api-permissions.png)
_API Grant Permissions Page_

This page shows you all the permissions the client requests. Click 'approve' to grant these permissions to this client.

## Reviewing your authorized clients

As a user, you can review all the clients that you've granted access to on the Manage Authorized Clients page. Click on the menu at the upper right corner, then click on 'Account'. At the bottom of the page you'll find the 'Manage Authorized Clients' link.

![API Manage Authorized Clients Page]({{ site-url }}/assets/api/api-manage-clients.png)
_Manage all the clients that currently have access to your account_

You can view the permissions that each client has (hover over the icon to see a description of each permission). You can also click 'Revoke' at any time to revoke the access of a client immediately.

---

# Example: autolab-cli

To help showcase the capabilities of the API, we developed autolab-cli: A first-party command line client that serves as both a practical tool for users of Autolab, as well as a reference design for developers intending to use the API in their own programs. We'll be using this program to demonstrate how to setup and use a client with Autolab. (For CMU users, this program, `autolab`, has already been installed on the andrew linux machines).

## Obtaining authorization

Make sure you have the cli installed by running `autolab` in your terminal. If you see the usage instructions you're good to go.

- To setup autolab-cli with your Autolab account, run `autolab setup`. This will initiate a manual activation.

![autolab-cli setup]({{ site-url }}/assets/api/api-cli-setup.png)
_What you'll see when you run `autolab setup`_

Once you approve the client on the Autolab website, the client will respond telling you that authorization was successful. You should be able to use the client from now on. If at any point you want to reset the client, run `autolab setup -f` and you'll be asked to re-authorize the client from a clean state.

## Viewing your courses and assessments

- To view your current courses, run  
```
$ autolab courses
```

![autolab-cli courses]({{ site-url }}/assets/api/api-cli-courses.png)

This will show you a list of courses in the form `unique_name (Display name)`. You should use the 'unique_name' of each course when interacting with autolab-cli.

- To view the assessments of a course, run  
```
$ autolab asmts <course_unique_name>
```

This will show you a list of assessments in the same `unique_name (Display name)` format.

## Downloading an assessment

- To start working on an assessment, go to a directory where you usually put your work, and run  
```
$ autolab download <course_unique_name>:<asmt_unique_name>
```

![autolab-cli download]({{ site-url }}/assets/api/api-cli-download.png)

This will create a directory with the assessment name in your current directory, and download the handout and writeup in it. This new directory is called an 'assessment directory'. Whenever you're inside an assessment directory, autolab-cli will respond according to the context.

For example, when you're inside an assessment directory, you can run  
```
$ autolab problems
```  
This will show you the problems of this assessment.

## Submitting solutions

- To submit to an assessment inside an assessment directory, run  
```
$ autolab submit <filename>
```

![autolab-cli submit]({{ site-url }}/assets/api/api-cli-submit.png)

Yep, it's that easy.

## Viewing scores

- To view the scores you got, run  
```
$ autolab scores
```

![autolab-cli scores]({{ site-url }}/assets/api/api-cli-scores.png)

The scores command will only return scores for those submissions that are made via this client. This is a privacy constraint of the Autolab API.

- To view the feedback you got, run  
```
$ autolab feedback
```

## Advanced features

- You can learn more about each sub-command by running  
```
$ autolab <sub-command> -h
```

This will reveal other flags you may be able to use with each command. For example, you can call all of the context-dependent commands outside of an assessment directory by providing the `<course_unique_name>:<asmt_unique_name>` pair.

---

This is our initial release of both the api and the cli, and we'd love to hear feedback from you! Feel free to email us if you have any questions or comments.  
Have an idea for a client? We'd be interested in discussing with you and providing support. We can't wait to see what kinds of new experiences awaits us in the future!


[api-docs]: https://autolab.github.io/docs/api-overview
[cli-repo]: https://github.com/autolab/autolab-cli