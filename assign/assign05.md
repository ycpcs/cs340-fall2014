---
layout: default
title: "Assignment 5: Parsing, Part 1"
---

*Note: preliminary, will change*

# Getting Started

Download [CS340\_Assign05.zip](CS340_Assign05.zip).

If you are using Counterclockwise under Eclipse, you can import the zipfile as an Eclipse project.

# Your Task

The source file `parser2.clj` implements a recursive descent parser for the following context-free grammar:

> *unit* → *statement\_list*
>
> *statement\_list* → *statement* *statement\_list* | *statement*
>
> *statement* → *var\_decl\_statement* | *expression\_statement*
>
> *var\_decl\_statement* → **var** **identifier** **;**
>
> *expression\_statement* → *expression* **;**
>
> *expression* → see below
>
> *primary* → **identifier** | **int\_literal** | **str\_literal**

The names and symbols in **bold** are terminal symbols (tokens), while the names in *italics* are nonterminal symbols.

Expressions are infix expressions with the := (assignment), +, -, \*, /, and ^ (exponentiation) operators.  They are parsed by [precedence climbing](../lectures/lecture06.html).

You have two tasks.  The first task is to add support for parenthesized sub-expressions.  This will involve modifying the **parse-primary** function.

The second task is to modify the parser to support two additional grammar productions:

> *statement* → **if** **(** *expression* **)** **{** *statement\_list* **}**
>
> *statement* → **while** **(** *expression* **)** **{** *statement\_list* **}**
