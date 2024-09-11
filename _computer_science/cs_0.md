---
layout: post
title: Algorithms and Data Structures
date: 2024-09-07 01:11:00-0400
tags: algorithms
tabs: true
---

I remember the first time I heard about algorithms while in school. I thought to myself: "Algorithms? Nice. I already know how to code." I was mistaken. I confused the instance with class, the application with the principle, the expression with the nature.

# Algorithms

To clarify what I mean by the above, I refer to the book [Algorithms (4th edition)](https://sedgewick.io/courses/algorithms/) by Sedgewick and Wayne in which I read a very thoughtful introduction to the subject. It says:

> When we write a computer program, we are generally implementing a _method_ that has been devised previously to solve some problem. This method is often independent of the particular programming language being used - it is likely to be equally appropriate for many computers and many programming languages. It is the method, rather than the computer program itself, that specifiies the steps tht we can take to solve the problem. The term _algorithm_ is used in computer sicence to describe a finite, deterministic, and effective problem-solving method suitable for implemetation as a computer program. Algorithms are the stuff of computer science: they are central objects of study in the field.

Many times we can think of methods that, although implementable as computer programs, we can use them as a sequence of steps using pen and paper and be able to solve the problem. Computer, of course, make this process much more efficient.

## Description vs Implementation

To highlight the distinguishion between an algorithm description and its implementation, here is the description of the **Factorial** algorithm:

To compute the factorial of a non-negative integer _n_ (denoted as _n_!), follow these steps:

1. If _n_ is 0, the factorial is defined as 1 (since 0! = 1).
2. If _n_ is greater than 0, multiply all the integers from 1 to _n_ together.
3. Start by multiplying 1 by 2, then multiply the result by 3, and continue multiplying by each successive number until you reach _n_.
4. The result after all these multiplications is the factorial of _n_.

Examples are often provided alongside the description to help clarify how the algorithm works:

For example, the factorial of 5 (denoted as 5!) is calculated as:

$$  
( 5! = 1 \times 2 \times 3 \times 4 \times 5 = 120 ).
$$

Notice that the above description can be implemented in many languages. The description won't change but implementations can (and often are) different depending on which language is being used. 

This description explains the factorial algorithm step by step, making it easy to understand the process of multiplying sequential integers to compute the factorial:

{% tabs group-name %}

{% tab group-name C++ %}

```c++
int factorial(int n) {
    if (n == 0) {
        return 1;
    } else {
        return n * factorial(n - 1); 
    }
}
```

{% endtab %}

{% tab group-name Python %}

```python
def factorial(n):
    if n == 0:
        return 1
    else:
        return n * factorial(n - 1)
```

{% endtab %}

{% tab group-name Ruby %}

```ruby
def factorial(n)
    (1..n).inject(:*) || 1
end
```

{% endtab %}

{% tab group-name JavaScript %}
```javascript
function factorial(n) {
    if (n === 0) {
        return 1;
    } else {
        return n * factorial(n - 1);
    }
}
```
{% endtab %}

{% tab group-name C# %}
```c#
static int Factorial(int n)
{
    if (n == 0)
    {
        return 1;
    }
    else
    {
        return n * Factorial(n - 1);
    }
}
```
{% endtab %}

{% tab group-name PHP %}
```php
function factorial($n) {
    if ($n == 0) {
        return 1;
    } else {
        return $n * factorial($n - 1);
    }
}
```
{% endtab %}

{% tab group-name Go %}
```go
func factorial(n int) int {
    if n == 0 {
        return 1 
    }
    return n * factorial(n-1)
}
```
{% endtab %}

{% endtabs %}

# Data Structures

In the majority of the cases, algorithms will require some form of data organization so we can devise computer programs more effectively. This data organization is known as _data structures_. Data structures are considered the byproducts/end products of algorithms and therefore, they must be understood if we want to understand algorithms.

Sedgewick and Wayne remark that "simple algorithms can give rise to complicated data structures and, conversely, complicated algorithms can use simple data structures."

# Goals

For small problems, we are typically concerned about **correctness**, that is, an algorithm that provides the right answer to any given problem. However, for extremely large and complex problems, we are also concerned wiht methods that are **efficient with respect to time and space**.

# Origins

As discussed by Horowitz, Sahni, and Rajasekeran in the book [Computer Algorithms (2nd edition)](https://www.amazon.com/Computer-Algorithms-Ellis-Horowitz/dp/0929306414/), the word _algorithm_ is derived from the name of a Persian polymath (or polyhistor), Abu Ja'far Mohammed ibn Musa al Khowarizmi (c. 825 A.D.) who wrote a textbook on mathematics. Several works of al Khowarizmi were recognized as the firt systematized solutions for many problems in mathematics.

# Definition of Algorithm

Horowitz, Sahni, and Rajasekeran provide the following definition of Algorithm:

> **Definition** [Algorithm]: An _algorithm_ is a finite set of instructions that, if followed, accomplishe a particular task. In addition, all algorithms must satisfy the following criteria:
>
> 1. **Input.** Zero ore more quantities are externally supplied.
> 2. **Output.** At least one quantity is produced.
> 3. **Definiteness.** Each instruction is clear and unambiguous.
> 4. **Finiteness.** If we trace out the instructions of an algorithm, then for all cases, the algorithm terminates after a finite number of steps.
>5. **Effectiveness.** Every instruction must be very basic so that it can be carried out, in principle, by a person using only pencil and paper. It is not enough that each operation be definite as in criterion 3; it also must be feasible.

In other posts, we will review a series of algorithms and data structures, as well their implementations.