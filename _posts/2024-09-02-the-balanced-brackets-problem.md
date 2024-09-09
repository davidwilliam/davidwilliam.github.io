---
layout: post
title: The Balanced Brackets Problem - The Ruby Way
date: 2024-09-02 10:11:00
description: The balanced brackets problem is a well-known problem in computer science and programming in general. It asks if a string of brackets is balanced and therefore valid, which means that the brackets are in the correct order and there are no unmatched brackets. Whovever works with Ruby for programming, will try to solve any problem in "the Ruby way", that is, with simplicity, elegance, and human-centered design. The Ruby Way can be described as a philosophy that encourages programmers to write code that is both powerful and easy to understand.
tags: ruby balanced brackets parentheses
categories: programming
comments: true
og_image: /assets/img/metaprogramming.jpg
---

The balanced brackets problem is a well-known problem in computer science and programming in general. It asks if a string of brackets is balanced and therefore valid, which means that the brackets are in the correct order and there are no unmatched brackets. Whovever works with Ruby for programming, will try to solve any problem in "the Ruby way", that is, with simplicity, elegance, and human-centered design. The Ruby Way can be described as a philosophy that encourages programmers to write code that is both powerful and easy to understand. In this post I review this problem and show why classic approach for solving it remains the best one yet, with advantages in performane that justifies its choice.

# Reviewing the Problem

Let's first review the problem: Given a string containing brackets - any sequence with some arrangement including `"(", ")", "[", "]", "{", "}"` - check if the brackets are opening and closing, and in the correct order. 

Examples of strings containing balanced brackets:

- `"{[()]}"`
- `"[()()]"`
- `"{[()]}()[]{}"`

Examples of strings containing unbalanced brackets: 

- `"{[("`
- `"{)"`
- `"[}"`
- `"{]}"`

and so on.

Typically, this problem allows empty strings to be valid ones. 

So let's summarize the constraints we need to consider before we implement a solution:

- Only strings containing brackets (round, curly, square) will be provided as input.
- Empty strings are permitted and are considered valid.
- The solution must output either `true` or `false`.

# Implementing a Solution

For solving this problem, imagine that I have a class named `BalancedBracket` and I will write a method named `check` to verify if any given string is valid. 

So I start by defining our class and method. Here, for simplicity, I will define `check` as a class method:

{% highlight ruby linenos %}
# balanced_bracket.rb
class BalancedBracket
    def self.check(string)
    end
end
{% endhighlight %}

Note: I could define `def self.check string` and it would work the same way since parenthesis are optional in Ruby in most cases. Here, I will leave the parenthesis while defining the method `check` for readability purposes. 

As I mentioned before, let's try to solve this problem in the Ruby Way. I know that empty strings are considered valid. So let's start with that:

{% highlight ruby linenos %}
# balanced_bracket.rb
class BalancedBracket
    def self.check(string)
        return true if string.empty?
    end
end
{% endhighlight %}

I can write our first test case and check our implementation so far. I will use MiniTest:

{% highlight ruby linenos %}
# balanced_bracket_test.rb
require "minitest/autorun"
require_relative "balanced_bracket"

class BalancedBracketTest < Minitest::Test
    def test_empty_string
        string = ""
        assert BalancedBracket.check(string)
    end
end
{% endhighlight %}

I can now run:

```
$ ruby -Itest balanced_bracket_test.rb
```

which will produce the following result:

```
Run options: --seed 54680

# Running:

.

Finished in 0.000258s, 3875.9675 runs/s, 3875.9675 assertions/s.

1 runs, 1 assertions, 0 failures, 0 errors, 0 skips
```

The first case of our problem is taken care off. Very simple, right? :-) 

I should now consider two things. The first is that, earlier, I mentioned that the balanced brackets problem is well-known in computer science which means that there is a standard way to approach it which is by using a stack. In data structures, a stack is a data structure that stores a linear, ordered sequence of items and operates on the LIFO process (Last In First Out).

The second is that I can leverage hash maps to identify the presence of brackets in our string as well as if there is a matching pair in the right order. Let's start by creating a variable to represent the stack and a variable to represent our hash map:

{% highlight ruby linenos %}
# balanced_bracket.rb
class BalancedBracket
    def self.check(string)
        return true if string.empty?

        stack = []
        brackets = { "(" => ")", "{" => "}", "[" => "]"}
    end
end
{% endhighlight %}

When invoked, the class method `check` from the class `BalancedBracket` will return `true` if the string is empty and ignore the rest of the code. If the string is not empty, it will create an empty array object to represent the stack and will create a hash object with all brackets under consideration for this problem: round, curly, and square. The order of the brackets in the hash object does not matter.

Now it's time to write the main logic of the `chech` method which will be located inside a loop as I iterate over the string that is passed as argument to the method `check`. Ruby offers many interesting ways to iterate over collection of objects. I choose `each_char`:

{% highlight ruby linenos %}
# balanced_bracket.rb
class BalancedBracket
    def self.check(string)
        return true if string.empty?

        stack = []
        brackets = { "(" => ")", "{" => "}", "[" => "]"}

        string.each_char do |char|
            # the main logic goes here
        end
    end
end
{% endhighlight %}

Almost there. The main logic is actually pretty simple. While iterating over `string`, I will check if there is a key `char` in `brackets`. If there is, I will add it (push it) to the `stack` array as a way of keeping track of the of the left brackets we found in the string. Recall that the keys in the `brackets` object are only left brackets and their values are the corresponding right brackets. In other words, if there is a key in `brackets` that is equal to `char`, that means we found a left bracket. If we cannot find a key for the given `char` value we receive in each iteration, that means we found a right bracket and now is time to check if the right bracket matches the last left bracket added to the stack. This is where the choice for using stacks is so relevant for solving this problem: the last item to enter the stack will be the first to leave it (LIFO). We will see soon why this is so important for performance purposes.

Back to the logic. If we cannot find a key in `brackets` that is equal to `char`, then this mean we found a right bracket. We just need to get the last left bracket added to `stack` (we pop it from the `stack` object) and compare if the corresponding matching value in our `brackets` equals to `char`. If not, we return false:

{% highlight ruby linenos %}
# balanced_bracket.rb
class BalancedBracket
    def self.check(string)
        return true if string.empty?

        stack = []
        brackets = { "(" => ")", "{" => "}", "[" => "]"}

        string.each_char do |char|
            if brackets.key?(char)
                stack.push(char)
            else
                left_bracket = stack.pop
                return false if brackets[left_bracket] != char
            end
        end
    end
end
{% endhighlight %}

Are we done? Not yet. Now, our method has the main logic but we need to write one final check before we finish it. As it currently is, the `check` method will iterate over `string` and check if there is a left bracket in it (`if brackets.key?(char)`) and if there is, it will add it to the `stack` object (`stack.push(char)`). If it does not find a left bracket (since we only accept strings containing round, curly, and square brackets), we found a right bracket which will be compared with the last left bracket we added to the `stack`. But what happens if the right bracket does not match the left bracket we just popped from the `stack`? Literally nothing. The loop will continue until it completes iterating over the input string. That mean that there will be a "residue" in the `stack`, that is, left brackets that were not matched with any right bracket. In other words, the stack won't be empty. That shows that the input string is not valid. Therefore, to complete our `check` method, we just need to check if `stack` is empty. The input string is only valid if after going through the loop, the `stack` object is empty:

{% highlight ruby linenos %}
# balanced_bracket.rb
class BalancedBracket
    def self.check(string)
        return true if string.empty?

        stack = []
        brackets = { "(" => ")", "{" => "}", "[" => "]"}

        string.each_char do |char|
            if brackets.key?(char)
                stack.push(char)
            else
                left_bracket = stack.pop
                return false if brackets[left_bracket] != char
            end
        end

        stack.empty?
    end
end
{% endhighlight %}

Recall that in Ruby, the last line of a method is what the method returns (unless there is no previous condition that makes the method to return something else). 

Now we are done! 

So let's update the test cases:

{% highlight ruby linenos %}
# balanced_bracket_test.rb
require "minitest/autorun"
require_relative "balanced_bracket"

class BalancedBracketTest < Minitest::Test
    def test_empty_string
        string = ""
        assert BalancedBracket.check(string)
    end

    def test_valid_case
        string1 = "{([])}"
        string2 = "{[]()}"
        assert BalancedBracket.check(string1)
        assert BalancedBracket.check(string2)
    end

    def test_invalid_cases
        string1 = "{([]"
        string2 = "{[(])}"
        refute BalancedBracket.check(string1)
        refute BalancedBracket.check(string2)
    end
end
{% endhighlight %}

When we run the tests:

```
ruby -Itest balanced_bracket_test.rb
```

we obtain:

```
Run options: --seed 21775

# Running:

...

Finished in 0.000276s, 10869.5558 runs/s, 18115.9263 assertions/s.

3 runs, 5 assertions, 0 failures, 0 errors, 0 skips
```

At this point, the solution correctly address the balanced brackets problem and all test cases we wrote are passing. However, every Rubyist I know will ask the question: "Is there a better way to solve this problem?". Something like "Can invoke the magic powers of the Ruby Way to solve this in a better way?"

The answer might be "it depends". It depends on what "better" means. This is what I show next. 

# An Alternative Solution

It goes without saying that if I decide to change the implementation of `check`, the tests must continue to pass in the same way. So let's try it.

Here is the logic that I will follow this time: I will continue to return true if the string is empty. After that, I will start a loop and get the string.legth at each iteration and call it `initial_length`. Then, I will check if there is any matching bracket next to each other. Something like `()`, `[]`, `{}`. If there is, I will replace it by an empty string (just another way to say we will remove it from the string). So I will check the length of the string after checking for matching brackets and call it `final_length`. If there is an iteration where `initial_legth` equals `final_length`, that means we don't have any matching brackets left in the string and it is time to exit the loop. Finally, we check if the string is empty:

{% highlight ruby linenos %}
# balanced_bracket.rb
class BalancedBracket
    def self.check(string)
        return true if string.empty?

        loop do
            initial_length = string.length
            string.gsub!(/\(\)|\[\]|\{\}/, '')
            final_length = string.length
            break if initial_length == final_length
        end

        string.empty?
    end
end
{% endhighlight %}

The method `gsub` allows us to use regular expressions and stands for "global substitution", that is, replace the pattern defined in the regular expression by the second parameter (in our case, an empty string). The symbol `!` means that we will evaluate the substitution and update the object `string` to its new state, discarding the previous one. This type of method is known in Ruby as "destructive method".

When we run the tests:

```
ruby -Itest balanced_bracket_test.rb
```

we obtain:

```
Run options: --seed 57734

# Running:

...

Finished in 0.000371s, 8086.2476 runs/s, 13477.0794 assertions/s.

3 runs, 5 assertions, 0 failures, 0 errors, 0 skips
```

The new implementation works. But is it better? Let's see.

## Thoughts on the Stack-Based Method

The stack-based method is straightforward and a standard approach to solving bracket validation problems. It’s easy to understand and maintain. The space complexity of the algorithm is `O(n)` as the stack grows with the number of unmatched opening brackets. Simple example: `((((((((((`. The time complexity is also `O(n)` as it iterates over `string` only once. Stack operations (push and pop) are very efficient and run in `O(1)`.

## Thoughts on the String-Based Method

The idea of modifying the string in place can be appealing for its simplicity, as it avoids the need for an additional data structure (stack). For this reason,  theoretically, it uses less memory compared to the stack-based method - `O(1)` instead of `O(n)` - as we can modify the input string in place without creating any new object that might grow as we iterate string.

However, the string-based has a potentially serious disadvantage: each time we remove a pair of brackets, the entire string needs to be reallocated (in most Ruby implementations), which can be inefficient. The time complexity of string manipulation operations like `gsub!` is `O(n)`, leading to an overall time complexity worse than `O(n)`. In fact, the exact time complexity depends on the number of times the loop runs, but in the worst case, it could approach `O(n²)` if many iterations are required.

# Pushing Even Further

It could be surprising (or even impressive) how many different ways one can come up to solve this problem in Ruby. I tried to solve it with a one-liner and I could not find (yet) a **true one-liner** to solve this. I say true one-liner because I do not consider a code that is thoughtlessly concatenated just so it is executed in one line to be a trye one-liner. Let's try something different:

{% highlight ruby linenos %}
# balanced_bracket.rb
class BalancedBracket
    def self.check(string)
        string.chars.each_with_object([]) { |char, stack| 
          if '([{'.include?(char)
            stack.push(char)
          elsif ')]}'.include?(char)
            return false if stack.empty? || '([{'.index(stack.pop) != ')]}'.index(char)
          end
        }.empty?
    end
end
{% endhighlight %}

In addition to `string.chars`, which returns a collection of chars from `string`, I am now using `each_with_object([])`, so we can pass an additional object to the block ("the body of the loop"). Remember when I said that Ruby had some interesting ways to iterate over collection of objects? This is just another example. The logic is really concentrated in loop block:

{% highlight ruby linenos %}
# in balanced_bracket.rb
if '([{'.include?(char)
    stack.push(char)
elsif ')]}'.include?(char)
    return false if stack.empty? || '([{'.index(stack.pop) != ')]}'.index(char)
end
{% endhighlight %}

By using `each_with_object`, we are creating a `stack` object and returning to the first version of the algorithm. Same principle, different way. 

But what about a **true one-liner**? I could not come up with one but I got close: 

{% highlight ruby linenos %}
# balanced_bracket.rb
class BalancedBracket
    def self.check(string)
        string.gsub!(/\(\)|\[\]|\{\}/, '') while string.sub!(/\(\)|\[\]|\{\}/, '')
        string.empty?
    end
end
{% endhighlight %}

When we run the tests

```
ruby -Itest balanced_bracket_test.rb
```

we see that our test cases pass:

```
Run options: --seed 16154

# Running:

...

Finished in 0.000351s, 8547.0058 runs/s, 14245.0097 assertions/s.

3 runs, 5 assertions, 0 failures, 0 errors, 0 skips
```

The `string.gsub!(/\(\)|\[\]|\{\}/, '')` searches for the first occurrence of any matching bracket pair `()`, `[]`, `{}`, which runs in `O(n)`. The code `while string.sub!(/\(\)|\[\]|\{\}/, '')` will continue until no more matching pairs are found. In the worst case, if the string contains many nested brackets, the loop might run `n/2` times, where each iteration reduces the string length by 2. Overall, the time complexity will be `O(n) * O(n/2) = O(n²)`.

# Conclusion

I am sure that are many other ways Rubyists can come up to solve this problem in the Ruby Way. However, it is important to verify if the underlying computations are indeed more efficient than the stack-based way. For solving this particular problem, stacks are widely used, well-understood, and reliable. Overall, the stack-based approach is efficient for most typical input sizes, with time complexity `O(n)` and space complexity `O(n)`. In our particular alternative method, we reduced the space complexity to `O(1)` (which is great) at the expense of increasing the time complexity to `O(n²)` (which is is bad). I recommend anyone to try different ways to solve well-known problems. It is not just fun but it also helps us better understand what is the best solution in each case. Here, we can see that the stack-based approach is the standard solution for a reason. 


