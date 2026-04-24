# 1669F_Eating_Candies.md

**Problem Link:** [https://codeforces.com/problemset/problem/1669/F](https://codeforces.com/problemset/problem/1669/F)

### 1. Problem Summary
Alice and Bob are eating candies from an array. Alice eats from left to right, and Bob eats from right to left. They can stop at any time. Find the maximum total number of candies they can eat such that the total weight of candies Alice eats equals the total weight Bob eats.

### 2. Core CS/DSA Concepts
Two Pointers, Prefix/Suffix Sums, Greedy approach.

### 3. Core Mathematical/Algorithmic Logic
Since Alice and Bob eat from opposite ends, their "territories" never overlap. We can represent this using two pointers: `left` starting at index 0 and `right` starting at index $n-1$.

We maintain `sumAlice` and `sumBob`.
1. If `sumAlice < sumBob`, Alice needs to eat more to catch up, so we move the `left` pointer.
2. If `sumBob < sumAlice`, Bob needs to eat more, so we move the `right` pointer.
3. If `sumAlice == sumBob`, we have a valid state. We record the total candies eaten ($(\text{left} + 1) + (n - \text{right})$) and then move both pointers (or just one) to find a potentially larger valid state.

The process stops when the pointers meet (`left > right`).

### 4. Time & Space Complexity
* **Brute Force:** $O(N^2)$ Time / $O(N)$ Space - Precalculating all prefix and suffix sums and using nested loops to find matching values.
* **Optimal:** $O(N)$ Time / $O(1)$ Space (excluding input storage) - The two-pointer approach visits each element exactly once.

### 5. Pseudocode
```text
For each testcase:
    Read n, array w
    left = 0, right = n-1
    sumAlice = w[0], sumBob = w[n-1]
    maxCandies = 0
    
    While left < right:
        If sumAlice == sumBob:
            maxCandies = (left + 1) + (n - right)
            // Move both to keep searching
            left++, right--
            If left <= right:
                sumAlice += w[left]
                sumBob += w[right]
        Else If sumAlice < sumBob:
            left++
            If left < right: sumAlice += w[left]
        Else:
            right--
            If left < right: sumBob += w[right]
            
    Print maxCandies
```

### 6. Actual C++ Code
``` cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n;
    cin >> n;
    vector<int> w(n);
    for (int i = 0; i < n; i++) cin >> w[i];

    int left = 0, right = n - 1;
    long long sumA = 0, sumB = 0;
    int ans = 0;

    while (left <= right) {
        if (sumA <= sumB) {
            sumA += w[left];
            left++;
        } else {
            sumB += w[right];
            right--;
        }

        if (sumA == sumB) {
            ans = max(ans, left + (n - 1 - right));
        }
    }
    cout << ans << "\n";
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    int t;
    cin >> t;
    while (t--) solve();
    return 0;
}
```

### 7. The Key Pattern Learned
**Converging Two Pointers:** When two entities process a linear structure from opposite ends to find a point of equilibrium or equality, the Two Pointers technique is the standard optimization. It avoids the $O(N^2)$ complexity of checking every possible "split point."

### 8. Key Math Learned
**Non-Overlapping Partitions:** In problems involving "prefix" and "suffix" properties meeting in the middle, the total count of elements is always $i + (n-j)$ where $i$ is the number of elements from the left and $(n-j)$ is the number of elements from the right.

### 9. Identification in Future Problems
* **Keywords:** "from left and right", "Alice and Bob", "equal total weight/sum".
* **Structural Triggers:** Two independent processes moving toward each other on a 1D array.
* **Constraint Triggers:** $N = 2 \cdot 10^5$ requires a linear $O(N)$ solution.

### GOOGLE SHEET SUMMARY
"two people eating from opposite ends", "sum from left equals sum from right", "maximize total elements" | Two Pointers ($O(N)$)
