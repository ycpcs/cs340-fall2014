---
layout: default
title: "Assignment 7: Parsing, Part 2"
---

*Preliminary, not official yet*

# Getting Started

Download [CS340\_Assign07.zip](CS340_Assign07.zip).

If you are using Counterclockwise under Eclipse, you can import the zipfile as an Eclipse project.

You should copy your `parser2.clj` file into the `src/minilang` directory.

# Your task

Your task is to transform the parse trees produced by your parser into [abstract syntax trees](../lectures/lecture06.html).  Do this by implementing the **build-ast** function in `astbuilder.clj`.

There are two things that your **build-ast** function should do:

* All nonterminal nodes that have a single child should be eliminated.
* **statement\_list** nodes should be simplified so that all of the statements are direct children of the statement list node.
