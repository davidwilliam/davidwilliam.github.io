---
layout: post
title: Selection Sort
date: 2024-09-10 10:11:00-0400
tags: sorting selection
---

The Selection Sort algorithm is another simple algorithm to understand and to implement. Given an `array`, it will iterate over it while keeping track of the index of the element with the smallest value. We will check all the non-repeating pairs that can be made with the elements of the array and at every iteration, we will check if the current first element is smaller than the second element. If it is, it will record the index of the smaller element. At every iteration of the outer loop, it will swap the current element with the element associated with the index that was just recorded. 

The name "Selection Sort" comes from the fact that the algorithm repeatedly selects the smallest (you can also do it with the largest) number in an array, compares it with all the other elements in the array, and then places it in the correct sorting order. 

In other words, after completing the first iteration of the outer loop (that is, we compare the first element of the array with all the others), we will have the smallest element in the first position. After completing the second iteration of the outer loop, we will have the second smallest element in the second position, and so on.

# Description

To sort a list of numbers in ascending order using the Selection Sort algorithm, follow these steps:

1. Start with the first number in the list and assume it is the smallest.
2. Compare this number with every other number in the list to find the actual smallest number.
3. Once the smallest number is found, swap it with the first number in the list.
4. Move to the second number in the list and repeat the process: compare it with all the remaining numbers to find the smallest, then swap it with the second number.
5. Continue this process for each position in the list, moving from left to right, finding the smallest number in the remaining unsorted portion, and placing it in its correct position.
6. Repeat these steps until the entire list is sorted.

# How It Works

Given the following array:

{% highlight c++ %}
array = [8,3,5,1,6]
{% endhighlight %}

We start at index `i = 0` and we check all the other elements `(j = 1..4)`, one by one, to see if the element at index `i` is smaller than the other element being compared. To help us do that, let's use `j` as variable for the second index. Since we won't compare the element with itself, we need to start `j` at the adjacent position of the index `i`. Therefore, we set `j = i + 1`, thus, `j = 1`.

To keep track of the index of the smallest value in the array, we define `min_index = i`, thus, `min_index = 0`.

We inspect `array[j]` and `array[min_index]` to see tnat they have the values `3` and `8`. Is `3 < 8`? Yes. Then we set `min_index = j`. Therefore, `min_index = 1`. 

Now, `j = 2`.  

We inspect `array[j]` and `array[min_index]` to see we have the values `5` and `3`. Is `5 < 3`? No. We don't have to do anything and `min_index` remains equal to `1`.

Now, `j = 3`.  

At `array[j]` and `array[min_index]` we have the values `1` and `3`. Is `1 < 3`? Yes. Then we set `min_index = j`. Therefore, `min_index = 3`. 

Now, `j = 4`.  

At `array[j]` and `array[min_index]` we have the values `6` and `1`. Is `6 < 1`? No. We don't have to do anything and `min_index` remains equal to `3`.

We got to the end of the array after `4` iterations. 

At this point, we swap the element at index `i` (the value `8`) with the element at index `min_index` (the value `1`), which will change our array to:

{% highlight c++ %}
array = [1,3,5,8,6]
{% endhighlight %}

Now, `i = 1`.

At this point, `j = 2`. We set `min_index = i`, thus, `min_index = 1`.

We have `array[j]` and `array[min_index]` with the values `5` and `3`. Is `5 < 3`? No. We don't have to do anything and `min_index` remains equal to `1`.

Now, `j = 3`.  We have `array[j]` and `array[min_index]` with the values `8` and `3`. Is `8 < 3`? No. We don't have to do anything and `min_index` remains equal to `1`.

Now, `j = 4`.  We have `array[j]` and `array[min_index]` with the values `6` and `3`. Is `6 < 3`? No. We don't have to do anything and `min_index` remains equal to `1`.

Now is the time to swap the element at index `i` with the element at index `min_index`. These two happen to be equal since `min_index` and `i` are both `1`. Therefore, even if we call the swap function, the array does not change:

{% highlight c++ %}
array = [1,3,5,8,6]
{% endhighlight %}

Now, `i = 2`.

At this point, `j = 3`. We set `min_index = i`, thus, `min_index = 2`.

We have `array[j]` and `array[min_index]` with the values `8` and `5`. Is `8 < 5`? No. We don't have to do anything and `min_index` remains equal to `2`.

Now, `j = 4`.  We have `array[j]` and `array[min_index]` with the values `6` and `5`. Is `6 < 5`? No. We don't have to do anything and `min_index` remains equal to `2`.

Once again, `min_index` and `i` are both `2` and therefore, if we call the swap function, the array does not change:

{% highlight c++ %}
array = [1,3,5,8,6]
{% endhighlight %}

Now, `i = 3`.

At this point, `j = 4`. We set `min_index = i`, thus, `min_index = 3`.

We have `array[j]` and `array[min_index]` with the values `6` and `8`. Is `6 < 8`? Yes. Then we set `min_index = j`. Therefore, `min_index = 4`. 

We swap the element at index `i` (the value `6`) with the element at index `min_index` (the value `6`), which will change our array to:

{% highlight c++ %}
array = [1,3,5,6,8]
{% endhighlight %}

And that's it, we completed the sorting of `array` by using the Selection Sort algorithm.


# Implementation

As always, everything we need to implement the algorithm should be found in the example above. If we let `n` be the size of `array`, we can see that for `n = 5`, for the first element of `array`, we iterated `4` times. For the second, `3` times. For the third, `2` times. For the fourth, `1` time. And then we completed the sorting. This shows we have two loops, one inside the other. The outer loop goes from `0` to `n - 2` and the inner loop goes from `i + 1` to `n - 1`. We write this as follows:

{% highlight c++ linenos %}
int n = array.size();
for (int i = 0; i < n - 1; i++) {
    // we will put the logic here
    for (int j = i + 1; j < n; j++) {
        // here
    }
    // and here
}
{% endhighlight %}

We are not done yet but the correct loop structure sets us up to most of the implementation of the Selection Sort algorithm. 

In our example we saw that as we start iterating in the outer loop, we set `min_index` to be `i`. This is the variable we will use to keep track of the position of the smallest element in `array` we find while we compare each element with the others. We add that right before the beginning of the inner loop:

{% highlight c++ linenos %}
int n = array.size();
for (int i = 0; i < n - 1; i++) {
    int min_index = i;
    for (int j = i + 1; j < n; j++) {
        // here
    }
    // and here
}
{% endhighlight %}

Inside the inner loop, we want to check if the element `array[j]` is smaller than `array[min_index]`. If it is, we update `min_index` and set it to `j`:

{% highlight c++ linenos %}
int n = array.size();
for (int i = 0; i < n - 1; i++) {
    int min_index = i;
    for (int j = i + 1; j < n; j++) {
        if (array[j] < array[min_index]) {
            min_index = j;
        }
    }
    // and here
}
{% endhighlight %}

Finally, once we complete the inner loop, we swap `array[i]` with `array[min_index]` since we found an element that is smaller than the one that is currently selected:

{% highlight c++ linenos %}
int n = array.size();
for (int i = 0; i < n - 1; i++) {
    int min_index = i;
    for (int j = i + 1; j < n; j++) {
        if (array[j] < array[min_index]) {
            min_index = j;
        }
    }
    std::swap(array[i], array[min_index]);
}
{% endhighlight %}

As we saw in our example, if `array[i]` and `array[min_index]` have the same value, swapping won't change anything. The algorithm above works and will correctly sort arrays using the Selection Sort approach. We can certainly add the condition so we will only compute the swap if necessary:

{% highlight c++ linenos %}
int n = array.size();
for (int i = 0; i < n - 1; i++) {
    int min_index = i;
    for (int j = i + 1; j < n; j++) {
        if (array[j] < array[min_index]) {
            min_index = j;
        }
    }
    if (min_index != i) {
        std::swap(array[i], array[min_index]);
    }
}
{% endhighlight %}

# Complexity Analysis

The Selection Sort algorithm will not know what the smallest element is until it completes the nested loops. Therefore, even in the best case (when the array is already sorted), the time complexity is `O(n²)`. The same happens in the average case (mixed order of elements), as well as in the worst case (when the array is sorted in the inverse order). In all cases, the time complexity is `O(n²)`.

Since we are performing an in-place sorting mechanism (not creating a new variable that grows with respect to the size of the array), the space complexity is constant, that is, `O(1)`.

# Properties

- **Time Complexity (Best):** `O(n²)` – Comparisons still occur for every element, regardless of sorting.
- **Time Complexity (Average):** `O(n²)` – Always performs the same number of comparisons.
- **Time Complexity (Worst):** `O(n²)` – No difference in time complexity, regardless of input.
- **Space Complexity:** `O(1)` – In-place algorithm with no additional memory usage.
- **Stability:** No – Can change the relative order of equal elements during swaps.
- **In-Place:** Yes – Sorting is done within the input array.
- **Comparison-Based:** Yes – Compares elements to find the smallest.
- **Adaptive:** No – Does not take advantage of existing order in the input.
- **Online:** No – Needs all input data to begin sorting.

# Conclusion

We will most likely not use the Selection Sort algorithm in real-world applications since there are more efficient sorting algorithms that perform better than `O(n²)` in both average and worst cases. However, Selection Sort can be useful for small datasets or in situations where minimizing the number of swaps is important. Its simplicity and clear structure also make it a good choice for educational purposes, helping us understand how more advanced algorithms improve on basic sorting techniques.

# Code

Selection Sort and other algorithms are available in a [DSA C++ library](https://github.com/davidwilliam/DSA) I created an maintain on GitHub. I am frequently adding more algorithms to this library.