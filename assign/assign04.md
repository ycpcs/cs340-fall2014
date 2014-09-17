---
layout: default
title: "Assignment 4: Clojure MOOC"
---

**Due**:

* Milestone 1 is due Monday, Sept 29th
* Milestone 2 is due Monday, Oct 6th

**Note**: This assignment will not be graded.

# Learning Clojure

We will be using [Clojure](http://clojure.org/) for a series of programming assignments.

The [Functional programming with Clojure](http://mooc.cs.helsinki.fi/clojure) MOOC at the University of Helsinki is a truly excellent way to learn Clojure.  In this assignment you will work on the first four chapters.  There are two Milestones:

* Milestone 1: Complete **Basic tools**, **Training day**, and **I am a horse in the land of booleans**
* Milestone 2: Complete **Structured data**

You can find the chapters on the [Material and course content](http://iloveponies.github.io/120-hour-epic-sax-marathon/index.html) page.

# Programming environment

The **Basic tools** chapter covers the software you will need.

I recommend using Eclipse with the [Counterclockwise](https://code.google.com/p/counterclockwise/) plugin.  You can install this through the Eclipse marketplace (search for "counterclockwise").

I also recommend installing the Local Terminal plugin: this allows you to run command line commands from within Eclipse, including running Clojure interactively.  You can find Local Terminal by choosing **Help&rarr;Install new software...**, then choosing the Luna (or Juno, etc.) update site, then choosing **General Purpose Tools&rarr;Local Terminal**.  Note that local terminal only works on Linux and Mac OS.  (If you are using Windows, you might consider running Linux in a virtual machine.)

As an alternative to Eclipse and Counterclockwise, you can try installing [Light Table](http://www.lighttable.com/).

The computers in KEC 119 have Eclipse with Counterclockwise and Local Terminal.

# Using Git

The programming activities for each chapter are in a Git repository on GitHub.  The recommended way of starting each chapter is to fork the repository, and then clone your fork.

For example, after forking the **training-day** repository, I would clone my fork using the command

    git clone git@github.com:daveho/training-day

# Testing your work

To run the unit tests for a programming activity:

1. Start a Local Terminal, click the "Connect" icon, then run the command <code>cd <i>path-to-repo</i></code>, where <i>path-to-repo</i> is the path to the local git repository containing your work.
2. Next, run the command <code>lein midje</code>.  The output will indicate how many tests passed and which tests (if any) failed.
