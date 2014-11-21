---
layout: default
title: "Implementing Control Flow"
---

*This is incomplete, but should be finished very soon*

This document discusses how to implement control flow (**if** and **while** statements) as extra credit for [Assignment 5](assign08.html).

<div class="callout"><b>Important</b>: Don't work on the extra credit unless the standard features of your code generator are completely working.</div>

# Adding comparison operators

Since **if** and **while** statements use conditions (expressions that evaluate to a boolean result), you will need to add some boolean operators.

Add the following patterns to your lexer (in `lexer.clj`):

{% highlight clojure %}
[#"^<=" :op_lte]
[#"^<" :op_lt]
[#"^>=" :op_gte]
[#"^>" :op_gt]
[#"^=" :op_eq]
[#"^!=" :op_neq]
{% endhighlight %}

Change the operator precedence and associativity rules in your parser (`parser2.clj`):

{% highlight clojure %}
(def precedence
  {:op_assign 0,
   :op_eq 1,
   :op_neq 1,
   :op_lt 2,
   :op_lte 2,
   :op_gt 2,
   :op_gte 2,
   :op_plus 3,
   :op_minus 3,
   :op_mul 4,
   :op_div 4,
   :op_exp 5})

(def associativity
  {:op_assign :right,
   :op_eq :left,
   :op_neq :left,
   :op_lt :left,
   :op_lte :left,
   :op_gt :left,
   :op_gte :left,
   :op_plus :left,
   :op_minus :left,
   :op_mul :left,
   :op_div :left,
   :op_exp :right})
{% endhighlight %}

You will need to update your AST builder (`astbuilder.clj`) to handle the new operators.  You can handle them in exactly the same way as the other binary operators.

# Evaluating comparison operators

You may make the following simplifying assumptions about the comparison operators:

* The condition of an **if** or **while** statement is guaranteed to be an expression involving one of the comparision operators
* No comparison operator will ever be used to compute a value

So, you can assume that you will never see constructs such as the following:

<pre>
if (a + b) {
    <i>stuff</i>
}

x := c < d;
</pre>

In the first example above, the result of the + operator is used as a condition.  The the second example, the result of the &lt; operator is assigned to a variable.

These assumptions allow us to take a simple approach to generating code for the comparison operators: the generated code for a comparison operator should jump to a location in the generated code if the result of the comparison is true, and should fall through to the next instruction if the result of the comparison is false.

Your generated code for a comparison operator should look something like the following:

<pre>
<i>generated code for left-hand side of comparison</i>
<i>generated code for right-hand side of comparison</i>
cmp
if<i>cond</i> <i>label_if_true</i>
</pre>

In the code above, <i>cond</i> should be filled in appropriately based on which MiniVM conditional branch instruction implements the desired comparison, e.g., `lte` if the comparison operator is &lt;=.

## Generating code for control flow constructs

When your code generator needs to generate code for a control-flow construct (i.e., **if** or **while**), it should generate a label representing where in the generated code the condition should branch to if the comparision evaluates as true.
