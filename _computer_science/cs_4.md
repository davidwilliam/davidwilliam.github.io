---
layout: post
title: Quick Sort
date: 2024-09-11 10:11:00-0400
tags: sorting quick
---

Quick Sort is a sorting algorithm based on the divide-and-conquer strategy. Although introducing some new components to how we sort elements in an array (in comparison to the Bubble Sort, Selection Sort, and Insertion Sort), it is still a simple algorithm to understand and implement. 

Given an array, we selct one of its element as the `pivot` so we can define two subarrays where to the left of the pivot we have elements less than the pivot, and to the right, those greater than the pivot.

Compared to the Bubble Sort, Selection Sort, and Insertion Sort algorithms, the Quick Sort has the best time complexity, specially in the best and average case: `O(n log n)`. In the worst case, the time complexity is the same as the aforementioned sorting algorithms: `O(n²)`.

# Description

To sort a list of numbers in ascending order using the Quick Sort algorithm, follow these steps:

1. Select one element from the list as the "pivot." This element will help partition the list. The pivot can be chosen randomly, or you can pick the first, last, or middle element.
2. Reorganize the list so that all elements smaller than the pivot come before it, and all elements larger than the pivot come after it. The pivot is now in its correct sorted position.
3. After partitioning, the list is divided into two sublists: one with elements smaller than the pivot and one with elements larger than the pivot. Apply the same process (choosing a pivot and partitioning) to each sublist.
4. Repeat the process until all sublists contain just one element. At this point, the entire list will be sorted.

# How It Works

Given the following array:

{% highlight c++ %}
array = [10, 7, 8, 9, 1, 5]
{% endhighlight %}

We begin by choosing a **pivot** element. In this case, we always select the last element of the array as our pivot. For the first call to `partition`, the pivot is `5`. The idea is to rearrange the array so that all elements smaller than the pivot are placed to its left, and all elements larger than the pivot are placed to its right. This process ensures that the pivot is placed in its correct sorted position in the array.

- We start with `low = 0`, `high = 5`, and `pivot = 5`.
- We initialize `i = low - 1 = -1`. `i` will keep track of the boundary for elements smaller than the pivot.
- Now we begin a loop where `j` goes from `low` to `high - 1`, that is, from `j = 0` to `j = 4`. The idea is to compare `array[j]` with `pivot` and, if `array[j] < pivot`, increment `i` and swap `array[i]` with `array[j]`.

- At `j = 0`, `array[j] = 10`. We compare `array[j]` with `pivot` and check if `10 < 5`. This is false, so we don’t increment `i` or swap anything.
- At `j = 1`, `array[j] = 7`. We check if `7 < 5`, which is also false. No increment, no swap.
- At `j = 2`, `array[j] = 8`. We check if `8 < 5`, which is false again. No changes.
- At `j = 3`, `array[j] = 9`. We check if `9 < 5`, still false. No changes.
- At `j = 4`, `array[j] = 1`. We check if `1 < 5`, which is true. So we increment `i` to `i = 0` and swap `array[i]` with `array[j]`. Now, we swap `array[0] = 10` with `array[4] = 1`.

Our array after the swap:

{% highlight c++ %}
[1, 7, 8, 9, 10, 5]
{% endhighlight %}

- The loop ends since we’ve reached `j = 4`. Now we place the pivot in its correct position by swapping `array[i + 1]` with `array[high]`. In this case, we swap `array[1] = 7` with `array[5] = 5`.

Our array after the pivot is placed correctly:

{% highlight c++ %}
[1, 5, 8, 9, 10, 7]
{% endhighlight %}

The pivot `5` is now in its correct place at index `1`. Now we recursively sort the subarrays to the left and right of the pivot.

The left subarray is `[1]`. Since it contains only one element, it’s already sorted. No further action is needed here.

Now we focus on the right subarray `[8, 9, 10, 7]`. We again choose the last element as the pivot. So, in this case, the pivot is `7`.

- We start with `low = 2`, `high = 5`, and `pivot = 7`.
- We initialize `i = 1` (since `low - 1 = 1`). We begin a loop where `j` goes from `low = 2` to `high - 1 = 4`.

- At `j = 2`, `array[j] = 8`. We check if `8 < 7`. This is false, so no increment or swap.
- At `j = 3`, `array[j] = 9`. We check if `9 < 7`. False again. No changes.
- At `j = 4`, `array[j] = 10`. We check if `10 < 7`. False. No changes.

After the loop, we swap `array[i + 1] = array[2] = 8` with `array[high] = array[5] = 7`.

Our array after placing the pivot `7` in its correct position:

{% highlight c++ %}
[1, 5, 7, 9, 10, 8]
{% endhighlight %}

The pivot `7` is now at index `2`. We now need to recursively sort the subarrays `[8, 9, 10]`.

Next, we sort the right subarray `[9, 10, 8]` by selecting `8` as the pivot. 

- We start with `low = 3`, `high = 5`, and `pivot = 8`.
- We initialize `i = 2`. We begin the loop where `j` goes from `low = 3` to `high - 1 = 4`.

- At `j = 3`, `array[j] = 9`. We check if `9 < 8`, which is false.
- At `j = 4`, `array[j] = 10`. We check if `10 < 8`, which is also false.

After the loop, we swap `array[i + 1] = array[3] = 9` with `array[high] = array[5] = 8`.

Our array after placing the pivot `8` in its correct position:

{% highlight c++ %}
[1, 5, 7, 8, 10, 9]
{% endhighlight %}

Now the pivot `8` is in its correct position at index `3`. We need to sort the remaining subarray `[10, 9]`.

Finally, we sort the subarray `[10, 9]` by choosing `9` as the pivot.

- We start with `low = 4`, `high = 5`, and `pivot = 9`.
- We initialize `i = 3`. The loop where `j` goes from `low = 4` to `high - 1 = 4` starts.

- At `j = 4`, `array[j] = 10`. We check if `10 < 9`, which is false.

After the loop, we swap `array[i + 1] = array[4] = 10` with `array[high] = array[5] = 9`.

Our array after placing the pivot `9` in its correct position:

{% highlight c++ %}
[1, 5, 7, 8, 9, 10]
{% endhighlight %}

At this point, the array is fully sorted.

# Implementation

As always, everything we need to implement the Quick Sort algorithm can be found in the example above.

We start by defining our main function `sort`. This function calls the partitioning process on the full array, and once the pivot is correctly placed, it recursively sorts the left and right parts of the array.

The first thing we need to do is call the `partition` function on the array between the `low` and `high` indices:

{% highlight c++ linenos %}
void sort(std::vector<int>& array, int low, int high) {
    if (low < high) {
        int pivotIndex = partition(array, low, high); 

        // Now we recursively sort the left and right subarrays
        sort(array, low, pivotIndex - 1);  // Sort left subarray
        sort(array, pivotIndex + 1, high); // Sort right subarray
    }
}
{% endhighlight %}

Next, we need to implement the partition function. As we discussed earlier, we are choosing the last element as the pivot. Our goal in this function is to rearrange the array so that all elements smaller than the pivot are placed to its left and all elements larger than the pivot are placed to its right. The pivot is then placed in its correct position.

First, we select the pivot as `array[high]`, which is the last element of the current subarray. We also initialize the index `i` as `low - 1`, which keeps track of the boundary for elements smaller than the pivot.

{% highlight c++ linenos %}
int partition(std::vector<int>& array, int low, int high) {
    int pivot = array[high];
    int i = low - 1;
    
    // We will add the rest of the logic here
}
{% endhighlight %}

Now, we loop through the array from `low` to `high - 1` (excluding the pivot element itself) and compare each element with the pivot. If an element is smaller than the pivot, we increment `i` and swap `array[i]` with `array[j]`, which moves the smaller element to the left side of the pivot.

{% highlight c++ linenos %}
int partition(std::vector<int>& array, int low, int high) {
    int pivot = array[high];
    int i = low - 1;

    for (int j = low; j < high; j++) {
        if (array[j] < pivot) {
            i++;
            std::swap(array[i], array[j]);  // Move smaller element to the left of the pivot
        }
    }

    // We will add the rest of the logic here
}
{% endhighlight %}

Once we have finished the loop, `i` will represent the last index where an element smaller than the pivot is placed. Now, we need to swap the pivot element (which is still at `array[high]`) with `array[i + 1]` to place the pivot in its correct sorted position.

{% highlight c++ linenos %}
int partition(std::vector<int>& array, int low, int high) {
    int pivot = array[high];
    int i = low - 1;

    for (int j = low; j < high; j++) {
        if (array[j] < pivot) {
            i++;
            std::swap(array[i], array[j]);
        }
    }

    std::swap(array[i + 1], array[high]); // Place the pivot in its correct position
    return i + 1;  // Return the pivot index
}
{% endhighlight %}

Once the `partition` function places the pivot in its correct position, the function returns the pivot index. We now have two subarrays, one to the left of the pivot and one to the right. We recursively call the `sort` function on these two subarrays.

Here is how we tie everything together in the `sort` function:

{% highlight c++ linenos %}
void sort(std::vector<int>& array, int low, int high) {
    if (low < high) {
        int pivotIndex = partition(array, low, high);

        // Recursively sort the left subarray
        sort(array, low, pivotIndex - 1);
        
        // Recursively sort the right subarray
        sort(array, pivotIndex + 1, high);
    }
}
{% endhighlight %}

---

And we are done. The Quick Sort algorithm is now implemented, and we can recursively sort the entire array by calling `sort(array, 0, array.size() - 1)`.

# Complexity Analysis

The time complexity of the Quick Sort algorithm varies based on the selection of the pivot and the arrangement of elements in the array. In the best case, where the pivot consistently splits the array into two nearly equal halves, the algorithm divides the problem size in half with each recursive call, resulting in a time complexity of `O(n log n)`. This is the optimal case for Quick Sort.

In the worst-case, if the pivot is always the smallest or largest element (such as when the array is already sorted in ascending or descending order), Quick Sort degenerates into a less efficient version of itself, performing `O(n²)` comparisons due to poor partitioning. This happens because the partition divides the array unevenly, leaving one subarray with nearly all the elements.

In the average-case, the time complexity of Quick Sort is `O(n log n)`, making it one of the fastest sorting algorithms in practice for a wide range of inputs, even though the worst-case time complexity is `O(n²)`.

The space complexity of Quick Sort is `O(log n)` due to the recursive calls made for partitioning. This space is used for the call stack during recursion. However, because Quick Sort is an in-place sorting algorithm, it does not require additional memory for sorting the array itself.

Despite the potential for a worst-case scenario, Quick Sort is highly efficient for large datasets, and with good pivot selection (such as choosing a random pivot or using the median-of-three method), it often outperforms other algorithms like Merge Sort in practice.

# Properties

- **Time Complexity (Best):** `O(n log n)` – Best performance with a well-chosen pivot.
- **Time Complexity (Average):** `O(n log n)` – Efficient for most datasets.
- **Time Complexity (Worst):** `O(n²)` – Worst case happens with poorly chosen pivots (e.g., when the pivot is always the smallest or largest element).
- **Space Complexity:** `O(log n)` – In-place sorting with minimal additional memory needed for recursion.
- **Stability:** No – May change the relative order of equal elements during partitioning.
- **In-Place:** Yes – Sorting is done within the input array.
- **Comparison-Based:** Yes – Compares elements to partition the array.
- **Adaptive:** No – Does not adapt based on input order.
- **Online:** No – Needs the full dataset before starting.

# Conclusion

Quick Sort is a highly efficient sorting algorithm that is widely used due to its average-case time complexity of `O(n log n)` and its in-place nature. Its divide-and-conquer approach, which repeatedly partitions the array around a pivot, allows it to sort large datasets quickly. Although Quick Sort has a worst-case time complexity of `O(n²)`, this can be mitigated by good pivot selection strategies, making it one of the most practical algorithms for general-purpose sorting. Its ability to handle large and diverse datasets efficiently, combined with its relatively low space requirements, makes Quick Sort a fundamental algorithm in computer science. Understanding Quick Sort helps us appreciate the balance between performance and potential pitfalls when sorting large datasets.

# Code

Quick Sort and other algorithms are available in a [DSA C++ library](https://github.com/davidwilliam/DSA) I created an maintain on GitHub. I am frequently adding more algorithms to this library.


