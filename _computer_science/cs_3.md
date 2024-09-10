---
layout: post
title: Insertion Sort
date: 2024-09-10 10:11:00-0400
tags: sorting insertion
---

The Insertion Sort algorithm is another simple algorithm to understand and to implement. We start by assuming that the first position of the array is already sorted and then we look to the next element and we check where that element should place to the left of the array. The process is very similar to holding a set of play cards in your hand and lift one card to place it in the correct place in case it is not already sorted. We repeat this process until we are done sorting all cards. Same thing happens with a given array. We assume the first element of the array is sorted, then check where to insert the next element. We will go all the way to the beginning of the array if necessary to place each element in the right position so we can have the array properly sorted.

The name Insertion Sort comes from the fact that you pick one element in the array and insert it in the right place. Contrary to Bubble Sort and Selection Sort, there is no swapping with the Insertion Sort. 

# Description

To sort a list of numbers in ascending order using the Insertion Sort algorithm, follow these steps:

1. Start with the second number in the list (since the first number is already considered "sorted").
2. Compare this number to the one before it. If it is smaller, move it left into its correct position, shifting the larger number to the right.
3. Move to the next number in the list and compare it to the numbers before it in the sorted portion.
4. Continue moving the number left, comparing it with each preceding number, until you find its correct position in the sorted part of the list.
5. Repeat this process for each remaining number in the list, moving them into their correct positions within the sorted portion.
6. Continue until the entire list is sorted.

# How It Works

Given the following array:

{% highlight c++ %}
array = [12, 11, 13, 5, 6]
{% endhighlight %}

As we mentioned, we consider that the first element of the array is already sorted. 

- We start with `i = 1`, `array[i] = 11`, `j = 0`.
- We select the element that we will check its position and insert it if not already correctly placed. We call this element `key` and we define it as `key = array[i]`. - We check if `key`, which is `11`, should be inserted to the left of the array. So we basically "loop backwards". In order to do the comparisons, since we want to move left, we set `j = i - 1`. 
- There is only one element to the left. Therefore we check if `array[j] > key`, that is, is `12 > 11`, which is true, then we set `array[j + 1] = array[j]` so the positions are changed. 
- Every time we iterate backwards, we need to decrease `j` by `1`. In this case `j` was `0`, we set `array[j]` in the adjacent position, that is, `array[j + 1]`, and now we decrease `j` by `1`, which turns it into `-1`. 
- What about the element in `key`? We insert it in the correct position: `array[j + 1] = key`. 
- Notice that when we set `array[j + 1] = array[j]`, the value defined in `key` is no longer in the array. That is why we need to add it back by doing `array[j + 1] = key`. 

Our array now looks like the following:

{% highlight c++ %}
[11, 12, 13, 5, 6]
{% endhighlight %}

- Now, `i = 2`, `array[i] = 13`, and `j = 1`. 
- Again, we set `key = array[i]` to keep record of it. 
- We loop backwards and check if `array[j]` is greater than `key`, which is not. 
- Computing `array[j + 1] = key` will just place `13` on its place, so it is not going to change the array. 

Our array now looks like the following:

{% highlight c++ %}
[11, 12, 13, 5, 6]
{% endhighlight %}

- Now, `i = 3`, `array[i] = 5`, and `j = 2`. 
- We set `key = array[i]` to keep record of it. 
- We loop backwards and check if `array[j]`is greater than `key`, which is. 
- Therefore we compute `array[j + 1] = array[j]`, which places `13` in the `j + 1` position. 
- We decrease `j` by 1 so `j = 1`. 
- We check again if `array[j]`is greater than `key`, which is. 
- We compute `array[j + 1] = array[j]`. 
- We decrease `j` by `1` so `j = 0`. 
- We check again if `array[j]`is greater than `key`, which is. 
- We compute `array[j + 1] = array[j]`. 
- Since j is `0`, we stop the backward loop. As part of the backward loop step, we always decrease `j` by `1`, so we end the loop with `j = -1`. 
- Now we need to insert the `key` in the right place `array[j + 1] = key` will just place `5` on its place, which is at position `0`. 

Our array now looks like this:

{% highlight c++ %}
[5, 11, 12, 13, 6]
{% endhighlight %}

- Now, `i = 4`, `array[i] = 6`, and `j = 3`. We set `key = array[i]` to keep record of it. 
- We loop backwards and check if `array[j]`is greater than `key`, which is. 
- Therefore we compute `array[j + 1] = array[j]`, which places `13` in the `j + 1` position (in place of `6`). 
- We decrease `j` by 1 so `j = 2`. 
- We check again if `array[j]`is greater than `key`, which is (`12 > 6`). 
- We compute `array[j + 1] = array[j]`. We decrease `j` by `1` so `j = 1`. 
- We check again if `array[j]`is greater than `key`, which is (`11 > 6`). 
- We compute `array[j + 1] = array[j]`. We decrease `j` by `1` so `j = 0`. 
- We check again if `array[j]`is greater than `key`, which is not (`5 > 6`). 
- So we stop the backward loop. 
- Now we need to insert the `key` in the right place `array[j + 1] = key` will just place `6` on its place, which is at position `1`. 

Our array now looks like this:

{% highlight c++ %}
[5, 6, 11, 12, 13]
{% endhighlight %}

And we are done. The array is now properly sorted.

# Implementation

As always, everything we need to know to implement the Insertion Algorithm should be easily found in the example above.

We start by defining the outer loop. We see that we go from `1` to `n - 1`:

{% highlight c++ linenos %}
void InsertionSort::sort(std::vector<int>& array) {
    int n = array.size();
    for (int i = 1; i < n; ++i) {
        // we will put the logic here
    }
}
{% endhighlight %}

The first thing we do at every iteration in the outer loop is to define what element is the `key` element and we define `j = i - 1` since we want to move backwards:

{% highlight c++ linenos %}
void InsertionSort::sort(std::vector<int>& array) {
    int n = array.size();
    for (int i = 1; i < n; ++i) {
        int key = array[i];
        int j = i - 1;

        // we will add the rest of the logic here
    }
}
{% endhighlight %}

Now, we want to loop backwards. We want to check if `array[j] > key` and if it is, we want to bring the element `array[j]` to the next position to the right of the array, that is, `array[j + 1]`. Since we are moving backwards, we decrease `j` by `1`:

{% highlight c++ linenos %}
void InsertionSort::sort(std::vector<int>& array) {
    int n = array.size();
    for (int i = 1; i < n; ++i) {
        int key = array[i];
        int j = i - 1;

        while (array[j] > key) {
            array[j + 1] = array[j];
            j--;
        }

        // we will add the rest of the logic here
    }
}
{% endhighlight %}

As we saw in our example, the value of `j` might go negative. For this reason, we want to check if `j` has a positive value to avoid going out of bounds in the array:

{% highlight c++ linenos %}
void InsertionSort::sort(std::vector<int>& array) {
    int n = array.size();
    for (int i = 1; i < n; ++i) {
        int key = array[i];
        int j = i - 1;

        while (j >= 0 && array[j] > key) {
            array[j + 1] = array[j];
            j--;
        }

        // we will add the rest of the logic here
    }
}
{% endhighlight %}

Now, we just need to place `key` in the right position, which will insert the value in `key` back into the array:

{% highlight c++ linenos %}
void InsertionSort::sort(std::vector<int>& array) {
    int n = array.size();
    for (int i = 1; i < n; ++i) {
        int key = array[i];
        int j = i - 1;

        while (j >= 0 && array[j] > key) {
            array[j + 1] = array[j];
            j--;
        }
        array[j + 1] = key;
    }
}
{% endhighlight %}

And we are done.

# Complexity Analysis

The time complexity of the Insertion Sort algorithm depends on the initial arrangement of elements in the array. In the worst-case scenario, where the array is sorted in reverse order, the algorithm compares and shifts each element for every iteration, which will result in a time complexity of `O(n²)`. In the best-case scenario, where the array is already sorted, Insertion Sort only performs comparisons without any shifts with a time complexity of `O(n)`. The average-case time complexity is `O(n²)` due to the nested loop structure. The space complexity of Insertion Sort is `O(1)` since it performs sorting in place without requiring extra memory. Despite its inefficiency for large datasets, Insertion Sort is often favored for small arrays or nearly sorted data because of its simplicity and lower overhead.

# Conclusion

Insertion Sort is simple sorting algorithm that can be used for small or nearly sorted datasets. Its intuitive approach of building a sorted section of the array by inserting elements into their correct positions makes it easy to understand and implement. While having a time complexity of `O(n²)`, which makes it less suitable for large datasets, its advantages in terms of simplicity and low overhead make it a useful tool for certain applications, particularly when the data is nearly sorted. By understanding the mechanics of Insertion Sort, we gain deeper insights into sorting techniques and their trade-offs.
