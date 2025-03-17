## Problem 1
You are given a string `s` consisting of only the characters 'a', 'b', and 'c'.  
Your task is to count the number of substrings that contain at least one occurrence of all three characters.

## Solution

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

int faxstring(string s) {
    int n = s.size();
    int l = 0, r = 0, c = 0;
    vector<int> v(3, 0); // To track occurrences of 'a', 'b', 'c'

    while (r < n) { // Expand `r` first
        v[s[r] - 'a']++;

        // Shrink the window from `l` until all 3 characters are present
        while (v[0] > 0 && v[1] > 0 && v[2] > 0) {
            c += n - r;  // Count valid substrings
            v[s[l] - 'a']--; // Remove left character
            l++; // Shrink left pointer
        }
        r++; // Expand right pointer
    }
    return c;
}

int main() {
    string s = "abcabc";
    int c = faxstring(s);
    cout << c << endl;
}
```


## Problem 2
You are given a sorted array `v` of integers and an integer `k`.  
Your task is to find the smallest maximum value `pos` such that you can select `k` elements from the array following this condition:
- You can select an element at index `i`, but the next selected element must be at index `i+2` or beyond.

## Solution

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Function to check if we can select `k` elements with max value <= `pos`
bool check(vector<int> v, int pos, int k) {
    int c = 0, i = 0;
    int n = v.size();

    while (i < n) {
        if (v[i] <= pos) { // If current element is within limit
            c++;           // Count it
            i += 2;        // Skip next element
        } else {
            i++;
        }
    }
    return c >= k;
}

// Function to find the minimum max value using Binary Search
int fun(vector<int> v, int k) {
    int l = *min_element(v.begin(), v.end()); // Smallest value in array
    int r = *max_element(v.begin(), v.end()); // Largest value in array
    int mid, ans;
    
    while (l <= r) {
        mid = l + (r - l) / 2;
        if (check(v, mid, k)) { // If `k` elements can be chosen with `mid` as max value
            ans = mid;  // Store the answer
            r = mid - 1; // Try for a smaller max value
        } else {
            l = mid + 1; // Increase the limit
        }
    }
    return ans;
}

int main() {
    vector<int> v = {2, 3, 5, 9}; // Given sorted array
    int k = 2; // Number of elements to choose
    int c = fun(v, k); // Find the answer
    cout << c << endl; // Print result
}
```


## Problem 3
You are given an array `v` where each element represents the length of a candle.  
Your task is to determine the **maximum possible length** of candles you can cut such that you get at least `k` candles in total.

## Solution

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Function to check if we can get at least `k` candles of length `pos`
bool check(vector<int> v, int pos, int k) {
    int c = 0;
    for (int i : v) {
        if (i >= pos) { // If the current candle length is greater than `pos`
            c += i / pos; // Count how many candles can be made of length `pos`
        }
    }
    return c >= k; // Return true if we can get at least `k` candles
}

// Function to find the maximum possible candle length using Binary Search
int candle(vector<int> v, int k) {
    int l = 1; // Minimum possible candle length
    int r = *max_element(v.begin(), v.end()); // Maximum candle length possible
    int maxe = 0;
    
    while (l <= r) {
        int mid = l + (r - l) / 2;
        if (check(v, mid, k)) { // If we can get `k` candles of length `mid`
            maxe = mid; // Store the maximum valid length
            l = mid + 1; // Try for a larger length
        } else {
            r = mid - 1; // Reduce the search space
        }
    }
    return maxe; // Return the maximum possible candle length
}

int main() {
    vector<int> v = {1, 8, 9}; // Given candle lengths
    int k = 3; // Number of candles needed
    int c = candle(v, k); // Find the maximum possible candle length
    cout << c << endl; // Print the result
}
```


## Problem 4
You are given an array `nums` and a list of `queries`.  
Each query modifies a range `[start, end]` of `nums` by adding a value `val`.  
Your task is to determine the **minimum number of queries** required to make all elements in `nums` reach their required values.  
If it is **not possible**, return `-1`.

## Solution

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Function to determine the minimum number of queries needed
int minZeroArray(vector<int>& nums, vector<vector<int>>& queries) {
    int n = nums.size(); // Store the size of nums array
    vector<int> diff(n + 1, 0); // Difference array for efficient updates
    int sum = 0; // Tracks cumulative update effect
    int pos = 0; // Tracks query position

    for (int i = 0; i < n; ++i) {
        // If the current sum at index i is not enough to match nums[i]
        while (sum + diff[i] < nums[i]) {
            if (pos == queries.size()) // No more queries available?
                return -1;

            int start = queries[pos][0];  // Query start index
            int end = queries[pos][1];    // Query end index
            int val = queries[pos][2];    // Value to add in range
            pos++; // Move to the next query

            if (end < i) continue; // Skip if the query doesn't affect index i

            // Apply range update using difference array
            diff[max(start, i)] += val;
            diff[end + 1] -= val;
        }
        sum += diff[i]; // Apply accumulated updates at index i
    }
    return pos; // Return the minimum queries used
}

int main() {
    vector<int> nums = {2, 2, 3}; // Target array
    vector<vector<int>> queries = {{0, 1, 1}, {1, 2, 2}, {0, 2, 1}}; // Queries modifying ranges
    int result = minZeroArray(nums, queries);
    cout << result << endl; // Output the minimum number of queries needed
}
```

## Problem Statement 5
You are given an array `v` of integers and an integer `k`.  
Your task is to determine the **minimum possible value** such that at least `k` elements in `v` satisfy a specific selection condition.  

### Conditions:
- If an element `v[i]` is **less than or equal to** the given `pos`, it is counted.
- Elements are **picked alternately** (i.e., if we select `v[i]`, we skip the next element).

## Solution

```cpp
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;

// Function to check if it's possible to pick at least 'k' elements
bool check(vector<int> v, int pos, int k) {
    int c = 0, i = 0;
    int n = v.size();

    while (i < n) {
        if (v[i] <= pos) {
            c++; // Count valid elements
            i += 2; // Skip the next element
        } else {
            i++;
        }
    }
    return c >= k; // Return true if we can pick at least 'k' elements
}

// Function to find the minimum possible value using binary search
int fun(vector<int> v, int k) {
    int l = *min_element(v.begin(), v.end()); // Minimum value in the array
    int r = *max_element(v.begin(), v.end()); // Maximum value in the array
    int mid, ans;

    while (l <= r) {
        mid = l + (r - l) / 2;
        if (check(v, mid, k)) { // Check if 'mid' can be a valid threshold
            ans = mid;
            r = mid - 1; // Try for a lower threshold
        } else {
            l = mid + 1; // Increase the threshold
        }
    }
    return ans;
}

int main() {
    vector<int> v = {2, 3, 5, 9}; // Input array
    int k = 2; // Number of elements to be selected
    int c = fun(v, k);
    cout << c << endl; // Output the minimum valid threshold
}
```

### Problem 6
You are given an array ranks, where each element represents the repair speed of a mechanic.
Your task is to find the minimum time required to repair cars number of cars.

### Conditions:
A mechanic with rank r takes r * t^2 time to repair t cars.
The goal is to distribute the work among mechanics optimally to minimize the total repair time.
We use binary search to efficiently determine the minimum possible time.

### Soultion
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath> // Required for sqrt function
using namespace std;

// Function to check if 'pos' time is enough to repair at least 'k' cars
bool check(vector<int> v, long long pos, long long k) {
    long long c = 0; // Count of cars repaired
    for (long long a : v) {
        c += sqrt(pos / a); // Calculate number of cars each mechanic can repair
    }
    return c >= k; // If we can repair at least 'k' cars, return true
}

// Function to find the minimum required time using binary search
long long repairCars(vector<int>& ranks, int cars) {
    long long l = 1; // Minimum possible time
    long long h = (long long) *max_element(ranks.begin(), ranks.end()) * cars * cars; // Upper bound estimate
    long long ans;
    long long mid;
    
    while (l <= h) {
        mid = l + (h - l) / 2; // Calculate the middle time
        if (check(ranks, mid, cars)) { // Check if we can repair all cars in 'mid' time
            ans = mid; // Store the possible answer
            h = mid - 1; // Try to minimize the time further
        } else {
            l = mid + 1; // Increase time if not enough cars are repaired
        }
    }
    return ans; // Return the minimum time required
}

// Driver function
int main() {
    vector<int> ranks = {4, 2, 3, 1}; // Repair speeds of mechanics
    int cars = 10; // Total number of cars to be repaired
    long long minTime = repairCars(ranks, cars);
    cout << minTime << endl; // Output the minimum time required
}
```

### Problem 7 
You are given an integer array nums consisting of 2 * n integers.

You need to divide nums into n pairs such that:

Each element belongs to exactly one pair.
The elements present in a pair are equal.
Return true if nums can be divided into n pairs, otherwise return false

### Solution
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool divideArray(vector<int>& nums) {
        vector<int> freq(501, 0); // Create an array of size 501 initialized to 0

        // Count frequency of each number
        for (int num : nums) {
            freq[num]++;
        }

        // Check if each number appears an even number of times
        for (int count : freq) {
            if (count % 2 != 0) { // If any number appears an odd number of times, return false
                return false;
            }
        }
        return true; // If all numbers appear an even number of times, return true
    }
};
```








