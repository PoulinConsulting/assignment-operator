# Introduction

I wrote this article in two parts.

First, you'll see how Python's `=` operator iterates over complex data structures.

Second, you'll discover how the `for` statement unpacks data using the same rules as the `=` operator.


## BNF notation
The [Python Language Reference](https://docs.python.org/2.7/reference/index.html) defines all the rules for the assignment statement using a modified form of [Backus Naur notation](http://cuiwww.unige.ch/db-research/Enseignement/analyseinfo/AboutBNF.html).  The Language Reference [explains how to read](https://docs.python.org/2.7/reference/introduction.html#notation) BNF notation. In short, 
* ***symbol_name ::=*** starts the definition of a symbol
* ***( )*** is used to group symbols
* ***+*** means appearing one or more times
* ***(a|b)*** means either a or b 
* ***[ ]*** means optional
* ***"text"*** means the literal text. For example, ***","*** means a literal comma character.



Here is the complete grammar for the [assignment statement](https://docs.python.org/2.7/reference/simple_stmts.html#assignment-statements).

---
An ***assignment statement*** consists of 
- one or more ***(target_list "=")*** groups 
- followed by either an ***expression_list*** or a ***yield_expression***

```
assignment_stmt ::=  (target_list "=")+ (expression_list | yield_expression)
```
---
A ***target list*** consists of
- a ***target***
- followed by zero or more ***("," target)*** groups
- followed by an optional ***trailing comma***

```
target_list     ::=  target ("," target)* [","]
```
---
Finally, a ***target*** consists of any of the following
- a variable name
- a nested target list enclosed in ( ) or [ ]
- a class or instance attribute
- a subscripted list or dictionary
- a list slice

```
target          ::=  identifier
                     | "(" target_list ")"
                     | "[" [target_list] "]"
                     | attributeref
                     | subscription
                     | slicing
```
---



## Multiple assignments

An assignment statement usually assigns one value to one variable
```
x = 0
```

but Python can combine these three assignments

```
x = 0
y = 0
z = 0
```
into one expression
```
x = y = z = 0
```


## Target lists

A target list is two or more targets separated by commas.
```
target, target, target, ... =
```
A target list lets you *unpack* data like so

```
x, y, z = 1, 2, 3
```

Remember that 
```
1, 2, 3
```
creates a *tuple*. This leads us to the next point.

## Iterable objects

Only *iterable objects* can be unpacked. When Python sees
```
x, y, z = 1, 2, 3
```
it *iterates* over the tuple and assigns its three values to x, y, and z.

Unpacking works with many iterable types
* tuples
* lists
* dictionaries
* strings
* ranges
* generators
* comprehensions
* file handles

## Starred targets (3.x only)

In Python 2.x the number of targets and values must match.

This code will produce an error
```
x, y, z = 1, 2, 3, 4   # too many values
```

Python 3.x introduced *starred variables*. Python first assigns values to the unstarred targets. After that, it forms a list of any remaining values and assigns it to the starred variable.

This code does not produce an error
```
x, *y, z = 1, 2, 3, 4   
# y == [2,3]
```


## Unpacking nested data



### Defining nested target lists with ( ) and [ ]

Target lists can be nested by enclosing the sub-list in either ( ) or [ ]. 

Python assigns a nested sequence to a nested target list by iterating over the nested sequence and assigning its values to the nested targets. 

Target lists can be nested to any depth and they are unpacked recursively.

```
a, b = 1, 2
# a == 1
# b == 2

a, b = ([1, 2], [3, 4])
# a == [1, 2]
# b == [3, 4]

a, (b, c) = (1, [2, 3])
# a == 1
# b == 2
# c == 3
```

## For loops

Everything you've just learned about assignments and nested targets also applies to for loops.

According to the [Language Reference](https://docs.python.org/3/reference/compound_stmts.html#the-for-statement)

>The `for` statement is used to iterate over the elements of a sequence (such as a string, tuple or list) or other iterable objects:


### Grammar

Here is the syntax of a `for` statement

```
for_stmt ::=  "for" target_list "in" expression_list ":" suite
              ["else" ":" suite]
```
Do the symbols `target_list` and `expression_list` look familiar? You saw them in the syntax of the assignment statement.

## For loop variables

### Standard rules for assignments

The Language Reference says
> The for statement is used to iterate over the elements of a sequence (such as a string, tuple or list) or other iterable objects ... Each item, in turn, is assigned to the target list using the **standard rules for assignments**

You already know the **standard rules for assignments**. You learned them earlier when we talked about the `=` operator. They are
* assignment to a single target
* assignment to multiple targets
* assignment to a nested target list
* assignment to a starred variable (Python 3.x only)


### Examples

I'll finish by showing you a few examples. 

```
# test data
negative_numbers = (-1, -2, -3, -4, -5)
positive_numbers = (1, 2, 3, 4, 5)
```

The `zip` function returns pairs of numbers.

I can loop over the pairs
```
for z in zip(negative_numbers, positive_numbers):
    print(z)
```
which produces this output
```
(-1, 1)
(-2, 2)
(-3, 3)
(-4, 4)
(-5, 5)
```

I can unpack the pairs if I wish
```
for (neg, pos) in zip(negative_numbers, positive_numbers):
    print(neg, pos)

-1 1
-2 2
-3 3
-4 4
-5 5
```
What about starred variables? This example finds a string's first and last character. The underscore character is often used when we need a dummy placeholder variable.
```
animals = [
    'bird',
    'fish',
    'elephant'
]

for (first_char, *_, last_char) in animals:
    print(first_char, last_char)

b d
f h
e t
```

## Conclusion

You can unpack nested data structures in sophisticated ways, but the syntax might seem complicated. I hope I've given you a clear picture of how it all works.
