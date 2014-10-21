restindex  
crumb: Assignment 4 format: rest page-title: Assignment 4: Parser, Part 1 encoding: utf-8 output-encoding: None initialheaderlevel: 2

/restindex

**Due: Friday, October 18th by 11:59 PM**

Getting Started
===============

Start with either the [example recursive-descent parser](../lecture/recursiveDescent.zip) or the [example precedence climbing parser](../lecture/precedenceClimbing.zip). (I suggest starting from the precedence climbing parser, but either is fine.)

Modify **Lexer.rb** so that it has the **PATTERNS** array that you created in [Assignment 3](assign03.html).

Your Task
=========

Implement a parser for a small programming language. Programs in this language are a series of statements, where each statement is either a variable declaration or an expression, followed by a semicolon.

Your parser should use the following context-free grammar. Note that terminal symbols are in **bold** and nonterminal symbols are in *italics*.

> *unit* → *statement\_list*
> *statement\_list* → *statement* *statement\_list* | *statement*
> *statement* → *var\_decl\_statement* | *expression\_statement*
> *var\_decl\_statement* → **var** **identifier** **;**
> *expression\_statement* → *expression* **;**
> *expression* → see below
> *primary* → **identifier** | **int\_literal** | **str\_literal**
> *primary* → **(** *expression* **)**

The *expression* nonterminal refers to binary infix expressions consisting of a sequence of *primary* expression separated by the following operators:

> <table>
> <col width="22%" />
> <col width="18%" />
> <col width="22%" />
> <thead>
> <tr class="header">
> <th align="left">Operators</th>
> <th align="left">Precedence</th>
> <th align="left">Associativity</th>
> </tr>
> </thead>
> <tbody>
> <tr class="odd">
> <td align="left"><strong>:=</strong></td>
> <td align="left">1</td>
> <td align="left">right</td>
> </tr>
> <tr class="even">
> <td align="left"><strong>+</strong> <strong>-</strong></td>
> <td align="left">2</td>
> <td align="left">left</td>
> </tr>
> <tr class="odd">
> <td align="left"><strong>*</strong> <strong>/</strong></td>
> <td align="left">3</td>
> <td align="left">left</td>
> </tr>
> <tr class="even">
> <td align="left"><strong>^</strong></td>
> <td align="left">4</td>
> <td align="left">right</td>
> </tr>
> </tbody>
> </table>
>
Each of the operators above combines two expressions. A *primary* expression (as can be seen in the grammar above) is a variable (identifier), integer literal, string literal, or an explicitly parenthesized expression.

When you run the **ParserTest.rb** program, it will parse the input as a *unit* and then print a parse tree. For example, consider the following input (note that you will need to type Control-D on a blank line to force the lexer to recognize that the end of the input has been reached):

    var a;
    var b;
    a := 4;    
    b := a ^ 2;
    a ^ (b + 3);

The program should produce a parse tree that looks something like:

    unit
    +--statement_list
       +--statement
       |  +--var_decl_statement
       |     +--var("var")
       |     +--identifier("a")
       |     +--semicolon(";")
       +--statement_list
          +--statement
          |  +--var_decl_statement
          |     +--var("var")
          |     +--identifier("b")
          |     +--semicolon(";")
          +--statement_list
             +--statement
             |  +--expression_statement
             |     +--op_assign
             |     |  +--primary
             |     |  |  +--identifier("a")
             |     |  +--primary
             |     |     +--int_literal("4")
             |     +--semicolon(";")
             +--statement_list
                +--statement
                |  +--expression_statement
                |     +--op_assign
                |     |  +--primary
                |     |  |  +--identifier("b")
                |     |  +--op_exp
                |     |     +--primary
                |     |     |  +--identifier("a")
                |     |     +--primary
                |     |        +--int_literal("2")
                |     +--semicolon(";")
                +--statement_list
                   +--statement
                      +--expression_statement
                         +--op_exp
                         |  +--primary
                         |  |  +--identifier("a")
                         |  +--primary
                         |     +--lparen("(")
                         |     +--op_plus
                         |     |  +--primary
                         |     |  |  +--identifier("b")
                         |     |  +--primary
                         |     |     +--int_literal("3")
                         |     +--rparen(")")
                         +--semicolon(";")

Hints
=====

Use recursive descent for the *unit*, *statement\_list*, *statement*, *var\_decl\_statement*, *expression\_statement*, and *primary* nonterminals.

It will probably be easiest to use precedence climbing for *expression*s. If you do not use precedence climbing, you will need to eliminate all of the occurrences of left recursion that would occur for left-associative operators (e.g., +, -, \*, etc.)

The parse trees created by your parser don't have to look exactly like mine, as long as they correctly encode the desired operator precedences and associativities.

There are couple places where the parser will need to choose between multiple productions. Here are some hints:

-   For parsing a *statement\_list*, the choice is whether or not to continue the *statement\_list*. After parsing one *statement*, use the lexer to peek for the next token: if there is a next token (the peek method didn't return **nil**), then parse another *statement\_list*.
-   For parsing a *statement*, the choice is between *var\_decl\_statement* and *expression\_statement*. Use the lexer to peek for the next token: if it is **var**, then use the *statement* → *var\_decl\_statement* production.

Testing
=======

This section lists some additional inputs you can try, along with possible parse tree outputs.

Note that depending on how your parser is implemented, you may see a different parse tree. However, the operator precedences and associativities in your parse tree should match mine.

Input:

    a * b + c;

Parse tree:

    unit
    +--statement_list
       +--statement
          +--expression_statement
             +--op_plus
             |  +--op_mul
             |  |  +--primary
             |  |  |  +--identifier("a")
             |  |  +--primary
             |  |     +--identifier("b")
             |  +--primary
             |     +--identifier("c")
             +--semicolon(";")

Input:

    a * (b + c);

Parse tree:

    unit
    +--statement_list
       +--statement
          +--expression_statement
             +--op_mul
             |  +--primary
             |  |  +--identifier("a")
             |  +--primary
             |     +--lparen("(")
             |     +--op_plus
             |     |  +--primary
             |     |  |  +--identifier("b")
             |     |  +--primary
             |     |     +--identifier("c")
             |     +--rparen(")")
             +--semicolon(";")

Input:

    a ^ b ^ c;

Output:

    unit
    +--statement_list
       +--statement
          +--expression_statement
             +--op_exp
             |  +--primary
             |  |  +--identifier("a")
             |  +--op_exp
             |     +--primary
             |     |  +--identifier("b")
             |     +--primary
             |        +--identifier("c")
             +--semicolon(";")

Input:

    (a ^ b) ^ c;

Output:

    unit
    +--statement_list
       +--statement
          +--expression_statement
             +--op_exp
             |  +--primary
             |  |  +--lparen("(")
             |  |  +--op_exp
             |  |  |  +--primary
             |  |  |  |  +--identifier("a")
             |  |  |  +--primary
             |  |  |     +--identifier("b")
             |  |  +--rparen(")")
             |  +--primary
             |     +--identifier("c")
             +--semicolon(";")

Submitting
==========

To submit, run the command

    make submit

from the directory containing the lexical analyzer files. Type your Marmoset username and password when prompted.

You can also create a zip file containing your code, and upload it to Marmoset as **assign04** using the web interface:

> <https://cs.ycp.edu/marmoset>
