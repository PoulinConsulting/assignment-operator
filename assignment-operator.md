
# How To Unpack Data With The = Operator And For Loops
## Use this simple "flip the script" technique to unpack data structures

# Introduction
Have you ever seen the `enumerate` function being used like this?
```
for (i,value) in enumerate(values):
   ...
```
Why does the `for` statement have two variables, and why are they written inside parentheses? This article answers those questions and many more. I wrote it in two parts

* First, you'll see how Python's `=` operator iterates over complex data structures.

* Second, you'll discover how the `for` statement unpacks data using the same rules as the `=` operator.


## BNF notation
This section is a bit technical, but it will help you understand the examples to come. The [Python Language Reference](https://docs.python.org/2.7/reference/index.html) defines all the rules for the assignment statement using a modified form of [Backus Naur notation](http://cuiwww.unige.ch/db-research/Enseignement/analyseinfo/AboutBNF.html).  The Language Reference [explains how to read](https://docs.python.org/2.7/reference/introduction.html#notation) BNF notation. In short, 
* ***symbol_name ::=*** starts the definition of a symbol
* ***( )*** is used to group symbols
* ***+*** means appearing one or more times
* ***(a|b)*** means either a or b 
* ***[ ]*** means optional
* ***"text"*** means the literal text. For example, ***","*** means a literal comma character.

Here is the complete grammar for the [assignment statement](https://docs.python.org/2.7/reference/simple_stmts.html#assignment-statements). It looks complicated because Python allows many different forms of assignment.

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

Mulitple assignment is a shorthand way of assigning the same value to many variables. An assignment statement usually assigns one value to one variable
```
x = 0
y = 0
z = 0
```

but Python can combine these three assignments into one expression
```
x = y = z = 0
```

## Flip the script with *target lists*
You've probably seen list [ ] and ( ) on the right side of an assignment statement to **pack** values into a data structure. But did you know that you can literally *flip the script* by writing a list of variables on the left side of the = sign? 

Using [ ] or ( ) on the left side of an = sign is called a ***target list***. For example
```
[target, target, target, ...] =
or
(target, target, target, ...) = 
```
Packing and unpacking are symmetrical and they can be **nested** to any level. You can use target lists to *unpack* data like so

```
# a flat data structure
flat_data = [1, 2, 3]
[x, y, z] = data

# a nested data structure
nested_data = [(1,2), (100, 200)]
[(a, b), (c, d)] = nested_data
```

Remember, lists and tuples are *iterable*. This is important because only *iterable objects* can be unpacked. When Python sees
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

Python assigns a nested sequence to a nested target list by iterating over the nested sequence and assigning its values to the nested targets. Nested arget lists are unpacked ***recursively***. Here are some more examples

```
a, b = 1, 2
# a == 1
# b == 2

a, b = ([1, 2], [3, 4])
# a == [1, 2]
# b == [3, 4]

(a, [b, c]) = (1, [2, 3])
# a == 1
# b == 2
# c == 3
```
## Test your knowledge
What are the values of a, b, c, x, y, and z?
```
(a, b, c) = (x, y, z) = 1, 2, 3
```
Hint: this expression uses both multiple assignment and unpacking.

## Starred targets (3.x only)

In Python 2.x the number of targets and values must match. This code will produce an error
```
x, y, z = 1, 2, 3, 4   # too many values
```

Python 3.x introduced *starred variables*. Python first assigns values to the unstarred targets. After that, it forms a list of any remaining values and assigns it to the starred variable. This code does not produce an error
```
x, *y, z = 1, 2, 3, 4   
# y == [2,3]
```

## Test your knowledge

Is there any difference between the variables `b` and `*b` in these two statements? If so, what is it?
```
(a, b, c) = 1, 2, 3

(a, *b, c) = 1, 2, 3
```

## For loops

Now that you know all about target list assignment, it's time to look at `for` loops. Let's examine the syntax of the `for` statement

```
for_stmt ::=  "for" target_list "in" expression_list ":" suite
              ["else" ":" suite]
```
Do the symbols `target_list` and `expression_list` look familiar? You saw them earlier in the syntax of the assignment statement. This has massive implications. ***Everything you've just learned about assignments and nested targets also applies to for loops.***

### Standard rules for assignments

The Language Reference says
> The for statement is used to iterate over the elements of a sequence (such as a string, tuple or list) or other iterable objects ... Each item, in turn, is assigned to the target list using the **standard rules for assignments**

You already know the **standard rules for assignments**. You learned them earlier when we talked about the `=` operator. They are
* assignment to a single target
* assignment to multiple targets
* assignment to a nested target list
* assignment to a starred variable (Python 3.x only)

In the introduction, I promised I would explain this code
```
for (i,value) in enumerate(values):
   ...
```
Now you know enough to figure it out yourself
* enumerate returns a sequence of `(number, item)` tuples
* when Python sees the target list `(i,value)` it unpacks `(number, item)` tuple into the target list

### Examples

I'll finish by showing you a few more examples. 

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
