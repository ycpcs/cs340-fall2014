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

Your task is to modify the parser to support the following productions:

> *primary* → **(** *expression* **)**
>
> *statement* → **if** **(** *expression* **)** **{** *statement\_list* **}**
>
> *statement* → **while** **(** *expression* **)** **{** *statement\_list* **}**

Supporting the first production (allowing parenthesized subexpressions) will require modifying the **parse-primary** function.  Supporting the other two productions will involve modifying the **parse-statement** function.

## Recursive descent parsing in a functional language

Implementing a recursive descent parser in a functional language is not really that hard.  However, there is one fundamental issue that requires some thought: the lexical analyzer (lexer) cannot implement *stateful* operations.

In our previous work with parsing (such as [Assignment 3](assign03.html)), we assumed that the lexer's **next()** method consumed a token, *removing it from the input sequence*, such that a subsequent call to **next()** would return a different token.  Because **next()** modified the lexer's internal state, it is a "stateful" operation.  When writing programs in a functional language, where destructive modifications of data are not possible, our operations can't be stateful.

From the standpoint of implementing a recursive descent parser, the issue is that each parse function needs to consume some number of tokens from the input sequence.  Thus, each time a parse function is called, we need to know how many tokens were consumed, and which tokens remain.  This turns out to be pretty easy if we have each parse function return *two* values: a parse node, and a sequence containing the remaining tokens.  We can have the parse functions return two values by having them return a *record* containing a parse node and the sequence containing the remaining tokens.

## Record data types

Record types in Clojure are very much like struct data types in C, except that record instances are immutable.

The parser defines three record types:

{% highlight clojure %}
; Record for parse tree nodes.
;
; symbol indicates the (terminal or nonterminal) symbol.
; value is the "value" of the node, which for terminal nodes
; is the lexeme of the token, and for nonterminal nodes
; is the list of child nodes.
(defrecord Node [symbol value])

; Result of expanding a single right-hand-side symbol:
; A single parse node, and a sequence containing the remaining
; input tokens.
(defrecord SingleParseResult [node tokens])

; Result of partially or completely applying a production:
; A sequence of 0 or more parse nodes, and a sequence containing
; the remaining input tokens.
(defrecord ParseResult [nodes tokens])
{% endhighlight %}

Constructing an instance of a record type is done as follows:

{% highlight clojure %}
(Node. :identifier "foobar")
{% endhighlight %}

This example creates a **Node** record with **:identifier** as the value of the **symbol** field, and the string **"foobar"** as the value of the **value** field.

Accessing a field of a record is done by applying a keyword naming the field that you want to retrieve as a function on the record instance.  For exmaple, if **n** is a **Node** record instance, then the expression

{% highlight clojure %}
(:symbol n)
{% endhighlight %}

would retrieve the value of **n**'s **symbol** field.

## Tokens, Parse functions

The parser takes its inputs as a sequence of tokens.  Each token is a two-element vector, where the first element is the token's lexeme, and the second element is the token's symbol, represented as a Clojure keyword value.  Here are the types of tokens:

> Symbol | Meaning
> ------ | -------
> **:var** | **var** keyword
> **:func** | **func** keyword
> **:if** | **if** keyword
> **:while** | **while** keyword
> **:identifier** | an identifier
> **:str\_literal** | a string literal
> **:int\_literal** | an integer literal
> **:op\_assign** | the assignment operator, **:=**
> **:op\_plus** | the addition operator, **+**
> **:op\_minus** | the subtraction operator, **-**
> **:op\_mul** | the multiplication operator, <b>*</b>
> **:op\_div** | the division operator, **/**
> **:op\_exp** | the exponentiation operator, **^**
> **:lparen** | left parenthesis, **(**
> **:rparen** | right parenthesis, **)**
> **:lbrace** | left brace, **{**
> **:rbrace** | right brace, **)**

Parse functions in the parser take a sequence of tokens and return a **SingleParseResult** record containing a **Node** (a parse tree representing the result of the parse), and a sequence containing the remaining input tokens.
