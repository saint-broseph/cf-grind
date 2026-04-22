# 1820B_Cyclic_AM_GM.md

**Problem Link:** [https://codeforces.com/problemset/problem/1820/B](https://codeforces.com/problemset/problem/1820/B)

### 1. Problem Summary
Find the maximum area of a rectangle consisting entirely of '1's within a grid that is formed by taking all cyclic shifts of a given binary string.

### 2. Core CS/DSA Concepts
Strings, Cyclic Arrays, Mathematical Optimization (AM-GM Inequality).

### 3. Core Mathematical/Algorithmic Logic
The problem looks like a 2D matrix maximal rectangle problem, but it collapses into 1D string analysis. Because the matrix is built from cyclic shifts, a contiguous block of $L$ '1's in the string forms a diagonal "staircase" or triangular shape of '1's in the matrix. 

If you have a contiguous block of exactly $L$ '1's, any rectangle of width $W$ and height $H$ that fits inside this shifted structure will always satisfy the constraint $W + H = L + 1$. 

To maximize the area ($W \times H$) subject to a constant sum ($W + H = \text{constant}$), we rely on the AM-GM (Arithmetic Mean-Geometric Mean) inequality. The product is maximized when $W$ and $H$ are as close to each other as possible. Therefore, we should choose $W = \lfloor(L+1)/2\rfloor$ and $H = \lceil(L+1)/2\rceil$. 

To find the maximum block of '1's taking the cyclic property into account, we simply append the string to itself ($s + s$) and find the longest contiguous sequence of '1's. The only edge case is if the string is entirely '1's, in which case the answer is simply $N \times N$.

### 4. Time & Space Complexity
* **Brute Force:** $O(N^2)$ Time / $O(N^2)$ Space - Explicitly generating the $N \times N$ matrix and running the standard "Maximal Rectangle in Binary Matrix" algorithm using histograms. Will TLE/MLE on $N = 10^5$.
* **Better:** $O(N)$ Time / $O(N)$ Space - Creating a new string of size $2N$ by concatenating $s + s$ to easily find the longest cyclic contiguous block of '1's, then applying the $O(1)$ math formula.
* **Optimal:** $O(N)$ Time / $O(1)$ Space - Counting the longest contiguous block of '1's using a two-pass loop over the original string without allocating extra memory for $s+s$, then applying the math formula.

### 5. Pseudocode
```text
For each testcase:
    Read string s
    n = length of s
    
    max_ones = 0
    current_ones = 0
    
    // Simulate s + s to handle cyclic wrap-around
    For i from 0 to 2*n - 1:
        If s[i % n] == '1':
            current_ones = current_ones + 1
            max_ones = max(max_ones, current_ones)
        Else:
            current_ones = 0
            
    // Edge case: the entire original string is '1's
    If max_ones >= n:
        Print n * n
    Else:
        // Apply AM-GM inequality logic
        L = max_ones
        W = (L + 1) / 2      // integer division (floor)
        H = (L + 2) / 2      // ceiling division logic
        Print W * H
```

### 6. Actual C++ Code
```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    string s;
    cin >> s;
    long long n = s.length();
    
    long long max_ones = 0;
    long long current_ones = 0;
    
    // Iterate up to 2*n to naturally simulate the cyclic array
    for (int i = 0; i < 2 * n; i++) {
        if (s[i % n] == '1') {
            current_ones++;
            max_ones = max(max_ones, current_ones);
        } else {
            current_ones = 0;
        }
    }
    
    // If the string is completely made of 1s, the max ones will hit 2*n.
    // The maximum possible rectangle in an n x n grid is just n * n.
    if (max_ones >= n) {
        cout << n * n << "\n";
    } else {
        // AM-GM: Maximize W * H given W + H = max_ones + 1
        long long w = (max_ones + 1) / 2;
        long long h = (max_ones + 2) / 2;
        cout << w * h << "\n";
    }
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    
    int t;
    cin >> t;
    while (t--) {
        solve();
    }
    
    return 0;
}
```

### 7. The Key Pattern Learned
**Cyclic Array Double-Pass Trick:** Whenever you are dealing with a cyclic string or circular array and need to find a contiguous subarray property, simulating an array of size $2N$ by iterating `i` from `0` to `2N-1` and accessing `A[i % N]` perfectly resolves the wrap-around logic without complex boundary conditions.

### 8. Key Math Learned
**AM-GM Inequality for Bounding Areas:** When the sum of two variables is bounded by a constant ($A + B = K$), their product ($A \times B$) forms a downward-facing parabola. The absolute maximum of this product occurs when $A$ and $B$ are as close to each other as mathematically possible.

### 9. Identification in Future Problems
* **Keywords:** "cyclic shifts", "circular array", "maximum area", "rectangle of 1s".
* **Structural Triggers:** Finding maximum properties across all possible shifts of an array strongly hints at linear string analysis over $A+A$ rather than generating the 2D shift matrix.
* **Math Triggers:** Anytime a problem asks you to maximize the product of two dimensions while their combination is restricted by a linear bound, bypass searching algorithms entirely and jump straight to $O(1)$ AM-GM centering.
