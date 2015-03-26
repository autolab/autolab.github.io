---
layout: post
title: "Autolab: Autograding for All"
date: 2015-03-26T04:14:30-04:00
author: 'Jake Zimmerman'
tags: ['release']
comments: true   # toggle this to disable comments
---

Let's face it: __teaching is hard__. There's the obvious time commitment
required for classes and lectures, but striving for excellence in education
necessarily requires countless hours spent outside of the classroom. This time
generally gets split between

- developing curriculum and assignments to stay relevant,
- meeting with students (in office hours, breakout sessions, tutoring, etc.),
- answering questions (whether in person or remotely),
- grading assignments, quizzes, and tests,

and many more areas. This list doesn't even begin to capture the inherently
difficult problem of adapting to meet students' unique needs, a challenge which
grows exponentially the more students there are.

Looking at that list of challenges above, one entry stands out: grading. Very
few people sit down to grade assignments with enthusiasm and vigor, which is
unfortunate; students can't learn from their mistakes until they know what they
are. The time required to grade fundamentally opposes student learning and
growth.

Teaching is hard, but at Autolab we help __make it easier__.


# Enhancing Learning

For programming classes, we drastically cut down the submission-to-feedback time
for students by exploiting autograding: programs evaluating other programs. In
this model, teachers create autograder programs that define a number of tests to
run on a student's submission to assign it a grade.

Autolab aims for flexibility: by utilizing user-customized Linux VMs,
instructors have absolute control over their autograding environment.
Autograders can be written in any language, using any software packages,
frameworks, compilers, databases---the possibilities are endless. No matter the
subject, Autolab can help make autograding a reality.

Because of this flexibility, students can receive feedback on their assignments
nearly instantaneously, closing the feedback loop in minutes rather than weeks.
This opens the door to iterative learning: students are alerted to incorrect
solutions immediately, enabling them to hone in on and fix troubling mistakes in
their code. Over the course of one assignment, this feedback loop can run any
number of times; each time, the student learns something above what they would
have had the assignment been graded manually.


# Fostering Community

In addition to kindling learning among individual students, we at Autolab aim to
foster a number of communities in all aspects of our work.


## Community among Classmates

Autolab provides class-wide scoreboards for autograded assignments. Scoreboards
are a fun and powerful motivation for students, and an excellent way to build
community. For the students at the top of the class, scoreboards encourage
refinement and healthy competition to keep up engagement. For other students,
scoreboards help to clearly outline what's required for full credit. Of course,
all names are anonymized by student-selected nicknames---some secretive and
others clever. In our experience, a mix of curiosity and competitiveness foster
a positive community everyone wins, regardless of skill level.


## Community among Educators

Because autograders are self-contained, Autolab can provide a new community for
instructors collaborating around designing, building, and iterating on quality
assignments and labs. In the future, we hope to develop a platform where
instructors can upload, discuss, share, and develop these assignments. This
would help foster a community where educators can help each other improve their
students' learning.


## Community among Users

We're dedicated to this mission of fostering community, so it was only natural
when it came to the decision of [open-sourcing Autolab][gh-autolab]. We actively
seek feedback from our users about how to improve, and we welcome contributions!
Head on over to [GitHub][gh-autolab] to browse the project source, read up on
documentation, report issues, or open pull requests. If you notice something
out-of-place or are dying to see a particular feature, please let us know! We
also actively read mail at the Autolab Dev mailing list (see the footer). We
love helping out as much as possible; this is only possible with your input.


# Autograding for All

Our vision is to bring Autolab and the benefits of autograding to all
programming and computer science classes, at the secondary- and
university-levels. At Carnegie Mellon, where Autolab was initially conceived and
is currently developed, we've seen Autolab's success in the classroom. Each
semester, we reach 3,000 undergraduate computer science students, amounting to
over 100,000 autograded jobs every semester.

Despite this, we've only scratched the surface of autograding's potential. If
you're interested in using Autolab for your class or school, reach out and we'd
be happy to help you get up and running. Also be sure to check out the [source
and documentation on GitHub][gh-autolab].



[gh-autolab]: https://github.com/autolab/Autolab
