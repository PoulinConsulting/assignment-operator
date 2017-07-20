# Introduction

Most of the time, an assignment statement assigns a single value to a single variable. But Python's `=` operator does more than just simple assignment. Used as an *iterator*, it can manipulate complex data structures with one or two lines of code.

The `for` statement also assigns values to variables. In the second part of this article I'll show you how to apply your new understanding of assignment to the `for` statement.


## BNF notation
The [Python Language Reference](https://docs.python.org/2.7/reference/index.html) defines all the rules for the assignment statement.

Python's grammar is defined using a modified form of [Backus Naur notation](http://cuiwww.unige.ch/db-research/Enseignement/analyseinfo/AboutBNF.html), which is similar to how regular expressions are written.  The Language Reference [explains how to read](https://docs.python.org/2.7/reference/introduction.html#notation) BNF notation. In short, 
* *** symbol_name ::= *** starts the definition of a symbol
* *** ( ) *** is used to group symbols
* *** + *** means appearing one or more times
* *** (a|b) *** means either a or b 
* *** [ ] *** means optional
* *** "text" *** means the literal text. For example, ***","*** means a literal comma character.



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



## Multiple assignment

You can assign the same value to multiple targets.

```
x = 0
y = 0
z = 0
```
can be written as
```
x = y = z = 0
```


## Target lists

A target list is two or more targets separated by commas.
```
target, target, target, ... =
```
Having more than one target in a target list lets you *unpack* data like so

```
x, y, z = 1, 2, 3
```

It's important to realize that 
```
1, 2, 3
```
actually creates a *tuple*. This lead us to  the next point.

## Iterable objects

Only *iterable objects* can be unpacked. When Python sees
```
x, y, z = 1, 2, 3
```
it *iterates* over the (1, 2, 3) tuple and assigns the values to the variables x, y, and z.

Python has many kinds of iterable objects, and all of them can be unpacked
* tuples
* lists
* dictionaries
* strings
* ranges
* generators
* comprehensions
* file handles

## Starred targets (3.x only)

In Python 2.x the number of targets in the list must match the number of values in the iterable object.

This code will produce an error
```
x, y, z = 1, 2, 3, 4   # too many values
```

Python 3.x introduced the idea of *starred variables*. Values are assigned to normal (unstarred) targets and any left over values are collected into a list and assigned to the starred variable.

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

Everthing you've just learned about assignment and nested targets can be applied to for loops.

According to the [Language Reference](https://docs.python.org/3/reference/compound_stmts.html#the-for-statement)

>The `for` statement is used to iterate over the elements of a sequence (such as a string, tuple or list) or other iterable object:


### Grammar

Here is the syntax of a `for` statement

```
for_stmt ::=  "for" target_list "in" expression_list ":" suite
              ["else" ":" suite]
```
Do the names `target_list` and `expression_list` look familiar? You saw them in the syntax of the assignment statement.

## For loop variables

### Standard rules for assignments

The Language Reference says
> *The for statement is used to iterate over the elements of a sequence (such as a string, tuple or list) or other iterable object ... Each item in turn is assigned to the target list using the **standard rules for assignments** *

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
What about starred variables? Here's an example that unpacks strings into separate characters. The underscore character is often used when we need a dummy placeholder variable.
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

Python has some sophisticated syntax for working with nested data structures, but this syntax can be confusing if you've never seen it before. I hope I've given you a clear picture of how it all works.

