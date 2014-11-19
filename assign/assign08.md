---
layout: default
title: "Assignment 8: Code Generation"
---

**Due**: Tuesday, Dec 9th by 11:59 PM

# Getting Started

Download [CS340\_Assign08.zip](CS340_Assign08.zip).

If you are using Counterclockwise under Eclipse, you can import the zipfile as an Eclipse project.

You should copy your `parser2.clj` and `astbuilder.clj` files from [Assignment 7](assign07.html) into the `src/minilang` directory.  Note that you will need to make a few changes to them as described below.

# Your Task

The goal of this project is to write a code generator that can take the ASTs produced from parse trees of minilang programs and generate a sequence of [MiniVM](https://github.com/daveho/MiniVM) instructions which carry out the computation specified by the program.

## First things first

The updated program has a new module, `node.clj`, which contains the `Node` datatype and functions for working with nodes.  You will need to modify your `parser2.clj` and `astbuilder.clj` files to use this new module.

For `parser2.clj`:

* Add `(:require [minilang.node :as node])` to the namespace (`ns`) declaration at the top of the file
* Comment out the definition of the **Node** data type (i.e., the `defrecord` which defines it)
* Change all occurrences of `Node.` with `node/make-node`

For `astbuilder.clj`:

* Add `(:require [minilang.node :as node])` to the namespace (`ns`) declaration at the top of the file
* Change all occurrences of `minilang.parser2.Node.` with `node/make-node` (there will probably only be one occurrence, in the `make-node` function)

## Augmented ASTs

This assignment introduces *augmented* ASTs.  An augmented AST has the same form as the basic ASTs produced by your `build-ast` function, but some of the nodes will have *properties*.  A node's properties can be used to store extra information about the node: specifically, information that will be useful during code generation.

The `analyzer.clj` module is provided to transform a plain AST into an augmented AST: the `analyzer/augment-ast` function does the transformation.  The analyzer adds three kinds of properties to the AST:

* `:statement_list` nodes will have an `:nlocals` property, specifying how many local variables are defined immediately within the statement list
* `:identifier` nodes will have a `:regnum` property, specifying an integer which uniquely identifies the variable named by the identifier
* the last `:expression_statement`, `:var_decl_statement`, `:if_statement`, or `:while_statement` node in the top-level statement list will have a `:last` property whose value is `true`

The `:regnum` property is very useful for generating code for assignments and variable references, since it tells you which MiniVM local variable corresponds to a program variable.

The `:last` property is useful to allow the code generator to emit code to print the result of the last statement in the top-level statement list: see "Code generation" below.

You will probably not need to use the `:nlocals` property for anything.

The `pp/pretty-print` function has been updated to print out property values.  For example, when the minilang program

    var a; a := 4 * 5; a;

is converted to an augmented AST and pretty printed, the output is

    :unit
    +--:statement_list :nlocals=1
       +--:var_decl_statement :regnum=0
       |  +--:identifier["a"]
       +--:expression_statement
       |  +--:op_assign
       |     +--:identifier["a"] :regnum=0
       |     +--:op_mul
       |        +--:int_literal["4"]
       |        +--:int_literal["5"]
       +--:expression_statement :last=true
          +--:identifier["a"] :regnum=0

Here is an example with multiple variables:

    var a; var b; var c; b := 6; c := 3; a := b*c;

This produces the following augmented AST:

    :unit
    +--:statement_list :nlocals=3
       +--:var_decl_statement :regnum=0
       |  +--:identifier["a"]
       +--:var_decl_statement :regnum=1
       |  +--:identifier["b"]
       +--:var_decl_statement :regnum=2
       |  +--:identifier["c"]
       +--:expression_statement
       |  +--:op_assign
       |     +--:identifier["b"] :regnum=1
       |     +--:int_literal["6"]
       +--:expression_statement
       |  +--:op_assign
       |     +--:identifier["c"] :regnum=2
       |     +--:int_literal["3"]
       +--:expression_statement :last=true
          +--:op_assign
             +--:identifier["a"] :regnum=0
             +--:op_mul
                +--:identifier["b"] :regnum=1
                +--:identifier["c"] :regnum=2

Notice that each variable is assigned a different `:regnum` property value.

## Code generation

The `generate-code` function takes an augmented AST as a parameter, and should print MiniVM instructions which execute the computation specified by the AST.  Note that `generate-code` does not need to return any specific value: it should just print the MiniVM instructions using the `println` function.

Here is the basic idea:

* For statement lists, recursively generate code for each child statement
* Nothing is required for var decl statements
* For expression statements, recursively generate code for the expression, then either pop the result value off by emitting a `pop` instruction (if the expression statement does not have the `:last` property), or print it by emitting `syscall $println` (if the expression statement does have the `:last` property)
* For binary operators other than `:op_assign`, recursively generate code for the two child sub-expressions, and then emit an appropriate arithmetic instruction (e.g., `add`, `sub`, `mul`, etc.)
* For `:op_assign`, recursively emit code for the right-hand-side expression, the emit an `stlocal` expression to store the result in a local variable, using the value of the `:regnum` property of the identifier to know which MiniVM local variable to store into
* For `:int_literal` nodes, emit an `ldc_i` instruction to load the constant integer onto the operand stack
* For `:identifier` nodes which appear in expressions, emit an `ldlocal` instruction, using the value of the `:regnum` property to know which MiniVM local variable to load from

## Stack management

You will need to think carefully about how to manage the MiniVM operand stack.  The basic idea is
that the code generated for each statement should not cause the operand stack either to grow or shrink.

## Hints

*Note: this section may be updated.*

The [MiniVM test programs](https://github.com/daveho/MiniVM/tree/master/t), [MiniVM documentation](https://github.com/daveho/MiniVM/blob/master/Documentation.md), and [MiniVM instruction reference](https://github.com/daveho/MiniVM/blob/master/InstructionSet.md) will probably be useful.

The functions in the `node.clj` module will be useful for working with augmented AST nodes:

* `node/has-prop?` checks whether a node has a particular property
* `node/get-prop` retrieves the value of a property from a node
* `node/children` retrieves the children of a node
* `node/num-children` returns a count of how many children a node has
* `node/get-child` returns a specified child (0 for the first child, etc.)

Each of these functions has a detailed comment explaining how to use it.

You can check the label of a node by applying the `:symbol` property to the node, e.g.

{% highlight clojure %}
(:symbol node)
{% endhighlight %}

would return `:expression_statement` if `node` is an expression statement node.

When printing, you can use the `str` function to concatenate multiple values into a single string.  For example,

{% highlight clojure %}
(println (str "\tldlocal " regnum))
{% endhighlight %}

would emit the instruction

    ldlocal 4

assuming that `regnum` has the value 4.

## Examples

Here are some tests programs and example outputs.

[TOOD: these will be here soon.]

# Submitting


