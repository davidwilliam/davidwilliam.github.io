---
layout: post
title: Bubble Sort
date: 2024-09-09 01:11:00-0400
tags: sorting bubble
---

Bubble Sort is perhaps one of the simplest sorting algorithms in computer science. Its logic consists of iterating over an array of elements comparing the element with index `i` and the element with index `(i + 1)`, swapping them if the first element (in the comparison) is not smaller than the second. The process is repeated comparing all pairs until the array is fully sorted. Therefore, it is clear we will need two loops, one inside the other. 

# Description

To sort a list of numbers in ascending order using the Bubble Sort algorithm, follow these steps:

1. Start at the beginning of the list and compare the first two numbers.
2. If the first number is larger than the second, swap them so that the smaller number comes first.
3. Move to the next pair of numbers and repeat the comparison, swapping them if necessary.
4. Continue this process for every pair of adjacent numbers until you reach the end of the list.
5. Once you reach the end, the largest number will have "bubbled up" to its correct position at the end of the list.
6. Repeat the process, starting from the beginning of the list again, but ignore the last number (as it is already in place).
7. Keep repeating this process, shrinking the unsorted portion of the list after each pass, until the entire list is sorted.

# How It Works

The Bubble Sort algorithm is super simple, easy to understand and to implement and for this reason it is used for teaching purposes. However, it is not an efficient algorithm since its time complexity in the average and worst case is `O(n²)`. Still, it is a great start to check how sorting algorithms work. 

Imagine that I want to sort the following array:

{% highlight c++ %}
array = [9,4,7,2,5]
{% endhighlight %}

We start by checking the first element `(9, i = 0)` and the adjacent element `(4, i = 1)`. Is `9 > 4`? Yes, then we swap the two elements:

{% highlight c++ %}
array = [4,9,7,2,5]
{% endhighlight %}

Next, we check the second element `(9, i = 1)` and the adjacent element `(7, i = 2)`. Is `9 > 7`? Yes, then we swap the two elements:

{% highlight c++ %}
array = [4,7,9,2,5]
{% endhighlight %}

Next, we check the third element `(9, i = 2)` and the adjacent element `(2, i = 3)`. Is `9 > 2`? Yes, then we swap the two elements:

{% highlight c++ %}
array = [4,7,2,9,5]
{% endhighlight %}

Next, we check the fourth element `(9, i = 3)` and the adjacent element `(5, i = 4)`. Is `9 > 5`? Yes, then we swap the two elements:

{% highlight c++ %}
array = [4,7,2,5,9]
{% endhighlight %}

After the first run, we have the largest element at the end of the array but no guarantees that the rest of the array is sorted. 

Notice that in the first loop, `i` went from `0` to `4` since we have `5` elements in the array. Since we are always comparing each element in the array with its adjacent element, we only ran the loop `4` times. 

Now, imagine that we are allowing `i` to start from `0` again. This time, we don't want to run the loop four times because we know that the largest element is already in the last place of the array. So we just need to run the loop `3` times:

We start by checking the first element `(4, i = 0)` and the adjacent element `(7, i = 1)`. Is `4 > 7`? No, so we don't do anything:

{% highlight c++ %}
array = [4,7,2,5,9]
{% endhighlight %}

Next, we check the second element `(7, i = 1)` and the adjacent element `(2, i = 2)`. Is `7 > 2`? Yes, then we swap the two elements:

{% highlight c++ %}
array = [4,2,7,5,9]
{% endhighlight %}

Next, we check the second element `(7, i = 2)` and the adjacent element `(5, i = 3)`. Is `7 > 5`? Yes, then we swap the two elements:

{% highlight c++ %}
array = [4,2,5,7,9]
{% endhighlight %}

And we stop since we already know that `9` is the largest number in the array and it is in the correct place.

Notice that in the second loop, `i` went from `0` to `3` and we looped three times.

Once again, we allow `i` to start from `0` again. This time, we don't want to run the loop three times because we know that the two largest elements are already in the two last places of the array. So we just need to run the loop `2` times:

We start by checking the first element `(4, i = 0)` and the adjacent element `(2, i = 1)`. Is `4 > 2`? Yes, then we swap the two elements:

{% highlight c++ %}
array = [2,4,5,7,9]
{% endhighlight %}

Next, we check the second element `(4, i = 1)` and the adjacent element `(5, i = 2)`. Is `4 > 5`? No, so we don't do anything.

And we stop. Notice that in the second loop, `i` went from `0` to `2` and we looped two times. We know that `5`, `7`, and `9` are correctly sorted.

We only have two elements left. We just need to check one time if they are sorted. Therefore, we allow `i` to start again from `0` again. 

We check the first element `(2, i = 0)` and the adjacent element `(4, i = 1)`. Is `2 > 4`? No, so we don't do anything.

Our final array is 

{% highlight c++ %}
array = [2,4,5,7,9]
{% endhighlight %}

which is properly sorted.

# Implementation

Everything we need to know to implement the Bubble Sort algorithm can be found in the example we just did. Notice that we start with a loop that goes from `0` to `4` which is `array.size - 1`. Then we repeatedly "allow" `i` to reset is value to `0` again. This is an indication that we are running one loop inside the other. This makes sense becaude we want check all numbers in the array agains the others, that is, check all pairs. Typically, a nested loop to check all pairs in `array` would be implemented as follows:

{% highlight c++ linenos %}
for (int i = 0; i < array.size(); i++) {
    for (int j = 0; j < array.size(); j++) {
        std::cout << array[i] << ":" << array[j] << std::endl;
    }
}
{% endhighlight %}

This will generate the following output:

```
9:9
9:4
9:7
9:2
9:5
4:9
4:4
4:7
4:2
4:5
7:9
7:4
7:7
7:2
7:5
2:9
2:4
2:7
2:2
2:5
5:9
5:4
5:7
5:2
5:5
```

This is clearly not what we want. We don't want to compare the numbers against themselves, for instance. Therefore, repeated pairs don't serve us any good. 

If we want to print only the non-repeating pairs, we would start the inner loop from `i + ` to avoid repetition:

{% highlight c++ linenos %}
for (int i = 0; i < array.size(); i++) {
    for (int j = i + 1; j < array.size(); j++) {
        std::cout << array[i] << ":" << array[j] << std::endl;
    }
}
{% endhighlight %}

This will generate the following output:

```
9:4
9:7
9:2
9:5
4:7
4:2
4:5
7:2
7:5
2:5
```

However, this is still not what we want. Here are the changes we need to apply: 

- Notice that in the above code, the outer loop runs `5` times (`i` goes from `0` to `4`). Since in each iteration we check the current element and the adjacent one, we just need to run the loop `4` times. Therefore, we need to change `for (int i = 0; i < array.size(); i++)` to `for (int i = 0; i < array.size() - 1; i++)`.
- Notice that we are supposed to swap positions if pairs are not already sorted (smaller to larger). This means that the elements might change places as we iterate over the array. As we saw in our example, what is in the first position of the array might change and we run the loop the second time. Therefore we need to start the inner loop at `0` as well so we don't miss anything. We need to change `for (int j = i + 1; j < array.size(); j++) {` to `for (int j = 0; j < array.size(); j++) {`.
- Notice that every time we loop over the array again we reduce the number of times we run the inner loop. For our example array of `5` elements, we started with `4` inner iterations, then `3`, then `2`, then `1`. This means that the `j` index will go from `0` to `array.size() - 1 - i`, that is, at each iteration of `i`, we will reduce `i` from the inner loop. 
- Instead of printing the pair, we will check if `array[j] > array[j + 1]` and if yes, we will swap their position in the array.

With these changes, we have our solution:

{% highlight c++ linenos %}
for (int i = 0; i < array.size() - 1; i++) {
    for (int j = 0; j < array.size() - i - 1; j++) {
        if (array[j] > array[j + 1]) {
            std::swap(array[j], array[j + 1]);
        }
    }
}
{% endhighlight %}

To avoid `array.size()` to be called multiple times, we precompute the size of the array:

{% highlight c++ linenos %}
int n = array.size();
for (int i = 0; i < n - 1; i++) {
    for (int j = 0; j < n - i - 1; j++) {
        if (array[j] > array[j + 1]) {
            std::swap(array[j], array[j + 1]);
        }
    }
}
{% endhighlight %}

And that's it. We have now a solution for the Bubble Sort algorithm. 

# Complexity Analysis

Generally speaking, every time we have have two nested loops, we know the time complexity is `O(n²)`. This is true in the average and worst case. But what if the algorithm is already sorted? That wold be the best case scenario. Could we optimize the solution to traverse the array only once and avoid further iterations if no swapping is necessary? Yes. Here are the steps of the changes we need to make in our code:

- We create a new variable `swapped` to track if a swap has occurred. 
- We set the variable to `false` in the outer loop.
- If the conditions for a swap are met, we set `swapped` to `true`.
- At the end of the outer loop, we check if `swapped` is false still (meaning, no swaps occurred). If it is, we break the loop.

{% highlight c++ linenos %}
int n = array.size();
bool swapped;
for (int i = 0; i < n - 1; i++) {
    swapped = false; 
    for (int j = 0; j < n - i - 1; j++) {
        if (array[j] > array[j + 1]) {
            std::swap(array[j], array[j + 1]);
            swapped = true; 
        }
    }

    if (!swapped) {
        break;
    }
}
{% endhighlight %}

If the array is already sorted, the outer loop will run only once and therefore the time complexity for the best case is `O(n)`.

No additional memory that is depended on the size of the array is required for running this algorithm and therefore the space complexity is `O(1)`.

# Conclusion

We will most likely not use the Bubble Sort algorithm in the real world since we have more efficient algorithms for sorting that run in time better than `O(n²)` in the worst case. However, Bubble Sort works fine for small arrays and for serving as a good starting point for us to understand why other sorting strategies are more efficient as the size of the array increases.

# Code

Bubble Sort and other algorithms are available in a [DSA C++ library](https://github.com/davidwilliam/DSA) I created an maintain on GitHub. I am frequently adding more algorithms to this library.