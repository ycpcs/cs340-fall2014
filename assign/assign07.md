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

There are three things that your **build-ast** function should do:

* All nonterminal nodes that have a single child should be eliminated.
* **:statement\_list** nodes should be simplified so that all of the statements are direct children of the statement list node.
* All terminal nodes should be eliminated, unless they contain information that is important.  For example, "punctuation" nodes such as **:lparen**, **:semicolon** should be eliminated, but **:identifier**, **:int\_literal**, and **str\_literal** nodes should be preserved

## Statements

In addition to the rules mentioned above, you should also eliminate **:statement** nodes.  Each type of statement should have a distinct symbol.

Make the following changes to your parser.  First, eliminate the following productions:

> *statement* → **if** **(** *expression* **)** **{** *statement\_list* **}**
>
> *statement* → **while** **(** *expression* **)** **{** *statement\_list* **}**

Add the following productions:

> *statement* → *if\_statement*
>
> *statement* → *while\_statement*
>
> *if\_statement* → **if** **(** *expression* **)** **{** *statement\_list* **}**
>
> *while\_statement* → **while** **(** *expression* **)** **{** *statement\_list* **}**

In your **parse-statement** function, just apply the *statement* → *if\_statement* production if the lookahead token is **:if**, and apply the *statement* → *while\_statement* production if the lookahead token is **:while**.

Making these changes will make it easy to create AST nodes for **:statement** parse nodes: all that will be required is constructing an AST node from the single child of the **:statement** node (since all statement nodes will now have a single child.)
