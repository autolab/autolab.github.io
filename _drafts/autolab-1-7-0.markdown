---
layout: post
title: "Autolab 1.7.0"
author: 'Jake Zimmerman'
# Some sample tags; you'll probably want to use different ones.
tags: ['release']
comments: true   # toggle this to disable comments
# Be sure to read _drafts/docs-markdown-style-guide/ :D
---

Hey everyone! We've been hard at work this summer getting Autolab primed for
the upcoming fall semester. Here's a quick rundown of what's new or changed in
this release.


# Annotate student-submitted PDFs

Now you can annotate student-submitted PDFs, just like you've always been able
to annotate their code submissions. Click through a student's submitted files to
a PDF file, and click anywhere on the PDF itself to bring up a box where you can
enter comments and assign point values. This feature is still really new, and
we're working on polishing it. Try it out for one of your assignments and let us
know what your impressions of it are! We'll be following up with more on how
this is implemented and a bit more about planned development in a future post.


# Deploy Autolab using Docker

The [Docker setup flow][autolab-docker] is now the recommended way of deploying
Autolab for single-classroom or low-volume courses. The process of getting a
fully-functioning Autolab setup is now much more repeatable. If you're
interested in setting up Autolab for your course or school, we encourage you to
take a look at these instructions, as they take a lot of the guesswork out of
setting everything up.


# Adding handouts to assessments is now more visible

Now you can add handouts to your assessments from the page where you view an
assessment's information. This should improve some of the pain of having to
click through lots of menus just to add a handout.


# ...and more!

This version also adds SQLite database support to ease development setup, fixes
a number of bugs that cropped up throughout the summer months, and adds a
preliminary version of "public classes" that can be used, amongst other things,
to host programming competitions and community-wide classes.


- - -

Have a feature request or issue that you'd like addressed? [Leave us a
note][issues].


[autolab-docker]: https://github.com/autolab/Autolab/wiki/Deploying-Autolab-with-Docker
[issues]: https://github.com/autolab/Autolab/issues/
