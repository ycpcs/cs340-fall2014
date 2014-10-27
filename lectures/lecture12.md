---
layout: default
title: "Lecture 12: Prolog"
---

Prolog is a *declarative programming language* based on logical inference. It is the main example of a language in the *logic programming* paradigm.

Download **tuProlog**, a Prolog interpreter written in Java:

> [tuProlog.zip](../resources/tuProlog.zip)

Extract the contents of the zip file, and double-click the file **bin/2p.jar** to start the Prolog interpreter. Or, cd to the **bin** directory and run the command

    java -jar 2p.jar

in a shell window.

Atoms
=====

Symbols:

    homer
    marge
    bart
    lisa
    maggie

Note: names of symbols *must* be written in lower-case letters.

Numbers:

> 1 2 3

Relations
=========

A relation is a table of facts. Here is a **father** relation

> <table>
> <col width="13%" />
> <col width="15%" />
> <tbody>
> <tr class="odd">
> <td align="left">homer</td>
> <td align="left">bart</td>
> </tr>
> <tr class="even">
> <td align="left">homer</td>
> <td align="left">lisa</td>
> </tr>
> <tr class="odd">
> <td align="left">homer</td>
> <td align="left">maggie</td>
> </tr>
> <tr class="even">
> <td align="left">grandpa</td>
> <td align="left">homer</td>
> </tr>
> <tr class="odd">
> <td align="left">grandpa</td>
> <td align="left">herb</td>
> </tr>
> </tbody>
> </table>

The first item in each tuple of the relation represents a person who is a father. The second item is a person who is a child of that father.

Relations can thus be formed as a collection of explicit facts, called *ground truths*. Each fact is a tuple belonging to the relation. In Prolog, we specify ground truths as

> relation ( *list of atoms* )

Here are some ground truths:

    father(homer, bart).
    father(homer, lisa).
    father(homer, maggie).
    father(grandpa, homer).
    father(grandpa, herb).

    mother(marge, bart).
    mother(marge, lisa).
    mother(marge, maggie).
    mother(grandma, homer).

Inference rules
===============

An inference rule allows new facts to be inferred from existing facts.

General form:

> *conclusion* :- *hypothesis*.

Facts and inference rules may use *variables*. A variable is a name, in upper-case letters, which stands for some possible member of a tuple in a relation.

Example: X is Y's paternal grandfather if there exists Z such that X is Z's father, and Z is Y's father:

    paternal_grandfather(X, Y) :- father(X, Z), father(Z, Y).

Note that X, Y, and Z are all variables, and the comma means "and" in the sense of a logical conjunction.

We can describe a paternal grandmother in a similar way:

    paternal_grandmother(X, Y) :- mother(X, Z), father(Z, Y).

Here is a possible set of inference rules:

    samefather(X, Y) :- father(Q, X), father(Q, Y).
    samemother(X, Y) :- mother(Q, X), mother(Q, Y).

    siblings(X,Y) :- samemother(X,Y); samefather(X,Y).

    paternal_grandfather(X, Y) :- father(X, Q), father(Q, Y).
    paternal_grandmother(X, Y) :- mother(X, Z), father(Z, Y).

Note the definition of the inference rule defining the **siblings** relation:

    siblings(X,Y) :- samemother(X,Y); samefather(X,Y).

The semicolon means "or" in the sense of logical disjunction. That is because two people are siblings if *either* they share the same father or mother.

Queries
=======

We can type in a potential fact, and based on the ground truths and the available inference rules, Prolog will attempt to find a derivation that proves that the fact is true.

Example:

    father(homer, bart).

This query is true because a ground truth matching the query exists.

The query

    father(marge, bart).

is false because this fact cannot be derived using the available ground truths and inference rules.

In general, answering a query requires constructing a chain of inferences. For example, the query

    siblings(bart, lisa).

is true because the facts

    mother(marge, bart).
    mother(marge, lisa).

are ground truths, enabling the query

    samemother(bart, lisa).

to be true if **marge** is substituted for the variable **Q** in the rule defining the **samemother** relation. This, in turn, is sufficient to deduce that

    siblings(bart, lisa).

is true.

A more interesting query

    siblings(homer, herb).

is true because

    father(grandpa, homer).
    father(grandpa, herb).

implies

    samefather(homer, herb).

which is sufficient to deduce that

    siblings(homer, herb).

is true. Note that the query

    samemother(homer, herb).

is false, because there is no derivation for this fact.

Declarative programming
=======================

Prolog is a declarative programming language because we never specify *how* we want a computation to be performed. We simply use ground truths and inference rules to describe a problem, and allow the inference algorithm to deduce a solution.

Declarative programming is nice because it allows us to specify a problem at a higher level.

Other declarative programming languages:

-   [Makefiles](http://en.wikipedia.org/wiki/Makefile), a language for directing the compilation of software
-   [SQL](http://en.wikipedia.org/wiki/Sql), the database query language

The language for Makefiles is especially interesting because, like Prolog, it is a logic programming language.