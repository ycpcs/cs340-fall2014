---
layout: default
title: "Lab 4: Recursive Descent Parsing"
---

# Getting Started

Download [RecursiveDescentJava.zip](../lecture/RecursiveDescentJava.zip).  Import it into your Eclipse workspace.

# Your task

Modify the parser so that it supports parenthesized expressions.

Do so by adding the following production:

> F -> ( E )

You will need to modify the lexer so that it supports left and right parentheses as terminal symbols.

You will also need to modify the **parseF** method in the parser class.

## Testing

Try running the program and entering the expression

> (a + b) * 3
