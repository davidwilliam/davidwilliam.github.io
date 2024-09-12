---
layout: post
title: Merge Sort
date: 2024-09-12 10:11:00-0400
tags: sorting merge
---

Merge Sort is a sorting algorithm that also employs the divide-and-conquer strategy, much like Quick Sort. Although it introduces a more complex mechanism involving merging arrays, it is still an intuitive and effective algorithm to understand. Merge Sort is particularly known for its stable nature and guaranteed `O(n log n)` time complexity across best, average, and worst cases. 

Given an array, the idea is to divide it into two halves, recursively sort those halves, and finally merge them back together in a sorted manner.

Compared to the Bubble Sort, Selection Sort, Insertion Sort, and even Quick Sort, the Merge Sort algorithm is a stable sorting technique. Unlike Quick Sort, which has a worst-case time complexity of `O(n²)`, Merge Sort ensures `O(n log n)` in all scenarios, making it a reliable choice, particularly for larger datasets.


# Description

To sort a list of numbers in ascending order using the Merge Sort algorithm, follow these steps:

1. Split the list into two halves, as equally as possible.
2. Recursively sort each half of the list.
3. Merge the two halves back together into a single sorted list. During the merge process, ensure that the smallest elements from each half are picked first.

# How It Works

Given the following array:

{% highlight c++ %}
array = [12, 11, 13, 5, 6, 7]
{% endhighlight %}

We begin by dividing the array into two halves. We then recursively sort each half before merging them back together. Let’s go through this step by step.

- First, we split the array into two halves: `[12, 11, 13]` and `[5, 6, 7]`.
  
Now, let's sort each half.

- For the left half `[12, 11, 13]`, we further split it into `[12]` and `[11, 13]`.
  - The leftmost subarray `[12]` is already sorted because it contains just one element.
  - Now we work with the right subarray `[11, 13]`. It gets split into `[11]` and `[13]`. Both subarrays are already sorted.

- Next, we merge `[11]` and `[13]` back together. We compare the elements and merge them to get a sorted array: `[11, 13]`.
- Now, we merge `[12]` with `[11, 13]`. We start by comparing `12` with `11`. Since `11 < 12`, we place `11` in the first position, followed by `12`, and finally `13`. The sorted left half is now `[11, 12, 13]`.

- For the right half `[5, 6, 7]`, we split it into `[5]` and `[6, 7]`.
  - `[5]` is already sorted. We then split `[6, 7]` into `[6]` and `[7]`, both of which are sorted.
  - Now, we merge `[6]` and `[7]` into a sorted array: `[6, 7]`.
  - Finally, we merge `[5]` with `[6, 7]`, comparing elements and placing them in order: `[5, 6, 7]`.

Now that we have two sorted halves, `[11, 12, 13]` and `[5, 6, 7]`, we merge them together.

- We compare the first element of each half. Since `5 < 11`, we place `5` first, followed by `6`, `7`, `11`, `12`, and finally `13`.

Our fully sorted array is now:

{% highlight c++ %}
[5, 6, 7, 11, 12, 13]
{% endhighlight %}

# Implementation

As always, everything we need to implement the Merge Sort algorithm can be found in the example above.

We start by defining our main function `sort`. This function recursively splits the array into two halves until the base case is reached, where each subarray has only one element. Then, the `merge` function is called to combine the sorted halves back together.

Let’s begin with the `sort` function:

{% highlight c++ linenos %}
void sort(std::vector<int>& array, int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;

        // Recursively sort the left and right subarrays
        sort(array, left, mid);
        sort(array, mid + 1, right);

        // Merge the sorted subarrays
        merge(array, left, mid, right);
    }
}
{% endhighlight %}

Next, we need to define the `merge` function. The goal of this function is to take two sorted subarrays (one from `left` to `mid`, and the other from `mid + 1` to `right`) and merge them back together into a single sorted array.

Here’s how we can implement the `merge` function:

{% highlight c++ linenos %}
void merge(std::vector<int>& array, int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;

    // Create temporary arrays to hold the left and right subarrays
    std::vector<int> leftArray(n1), rightArray(n2);

    // Copy data into the temporary arrays
    for (int i = 0; i < n1; i++)
        leftArray[i] = array[left + i];
    for (int i = 0; i < n2; i++)
        rightArray[i] = array[mid + 1 + i];

    // Merge the temporary arrays back into the original array
    int i = 0, j = 0, k = left;

    while (i < n1 && j < n2) {
        if (leftArray[i] <= rightArray[j]) {
            array[k] = leftArray[i];
            i++;
        } else {
            array[k] = rightArray[j];
            j++;
        }
        k++;
    }

    // Copy any remaining elements from the left and right arrays
    while (i < n1) {
        array[k] = leftArray[i];
        i++;
        k++;
    }

    while (j < n2) {
        array[k] = rightArray[j];
        j++;
        k++;
    }
}
{% endhighlight %}

Now, we have our `sort` and `merge` functions fully implemented.

# Complexity Analysis

The time complexity of the Merge Sort algorithm is consistently `O(n log n)` in all cases—best, average, and worst. This is because the array is always split in half at each step, resulting in `log n` levels of recursion, and at each level, merging the two halves takes `O(n)` time.

Merge Sort’s **space complexity** is `O(n)` because it requires additional space for the temporary arrays used during the merging process. Unlike Quick Sort, which is in-place, Merge Sort requires extra memory proportional to the size of the input array.

Although Merge Sort guarantees a time complexity of `O(n log n)` and is stable (preserves the relative order of equal elements), its space complexity makes it less efficient for large datasets compared to in-place algorithms like Quick Sort.

# Properties

- **Time Complexity (Best):** `O(n log n)` – Always splits the array and merges efficiently.
- **Time Complexity (Average):** `O(n log n)` – Works equally well for any arrangement of elements.
- **Time Complexity (Worst):** `O(n log n)` – Guaranteed consistent performance.
- **Space Complexity:** `O(n)` – Requires extra memory for merging the subarrays.
- **Stability:** Yes – Equal elements retain their relative order after sorting.
- **In-Place:** No – Requires additional memory for the merge process.
- **Comparison-Based:** Yes – Compares elements to merge them in sorted order.
- **Adaptive:** No – Always divides the array, regardless of the initial arrangement.
- **Online:** No – Needs the entire dataset before sorting can begin.

# Conclusion

Merge Sort is a stable and efficient sorting algorithm with a consistent time complexity of `O(n log n)`. Although it requires additional space for merging, its reliability and stability make it a solid choice, particularly for larger datasets where stability is important. Understanding the Merge Sort algorithm gives us insight into how divide-and-conquer strategies work in sorting and provides a comparison point with other algorithms like Quick Sort, which also follow a similar strategy but with different trade-offs.

# Code

Merge Sort and other algorithms are available in a [DSA C++ library](https://github.com/davidwilliam/DSA) I created and maintain on GitHub. I am frequently adding more algorithms to this library.