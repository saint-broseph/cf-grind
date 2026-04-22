# 1808B_Sorting_Contribution.md

**Problem Link:** [https://codeforces.com/problemset/problem/1808/B](https://codeforces.com/problemset/problem/1808/B)

### 1. Problem Summary
Calculate the sum of absolute differences between all pairs of cards for each coordinate (column) independently across $n$ cards with $m$ dimensions.

### 2. Core CS/DSA Concepts
Sorting, Prefix Sums, Combinatorics.

### 3. Core Mathematical/Algorithmic Logic
The total winnings is the sum of $|c_{i,k} - c_{j,k}|$ for all pairs of cards $(i, j)$ across all dimensions $k$. Since each dimension is independent, we can solve the problem for each column separately and sum the results.

For a single column of values, we need to calculate $\sum_{i=1}^{n} \sum_{j=i+1}^{n} |a_i - a_j|$. The absolute difference makes the order of $i$ and $j$ irrelevant. If we sort the column in non-decreasing order, the formula simplifies to $\sum_{i=1}^{n} \sum_{j=i+1}^{n} (a_j - a_i)$. 

In a sorted array of size $n$, the element $a_i$ (0-indexed) is subtracted $(n - 1 - i)$ times and added $i$ times. 
Mathematically, the contribution of $a_i$ to the total sum is:
$a_i \times (i - (n - 1 - i)) = a_i \times (2i - n + 1)$.
By sorting each column, we can compute the sum for that column in $O(n)$ time.

### 4. Time & Space Complexity
* **Brute Force:** $O(m \cdot n^2)$ Time / $O(n \cdot m)$ Space - Iterating through every pair of cards for every column. With $n, m = 3 \cdot 10^5$, $n^2$ is too slow.
* **Optimal:** $O(m \cdot n \log n)$ Time / $O(n \cdot m)$ Space - Sorting each of the $m$ columns independently ($n \log n$ per column) and then calculating the sum in linear time.

### 5. Pseudocode
```text
For each test case:
    Read n, m
    Read n rows and m columns into a 2D structure (or m separate vectors)
    
    total_sum = 0
    
    For each column j from 0 to m-1:
        Extract all n values of column j into a temporary list
        Sort the list in non-decreasing order
        
        column_sum = 0
        For i from 0 to n-1:
            // Element at index i is added i times and subtracted (n-1-i) times
            multiplier = i - (n - 1 - i)
            column_sum += list[i] * multiplier
            
        total_sum += column_sum
        
    Print total_sum
```
### 6. Actual C++ Code
```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

void solve() {
    int n, m;
    cin >> n >> m;
    
    // We store columns because calculations are independent per column
    vector<vector<int>> cols(m, vector<int>(n));
    
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> cols[j][i];
        }
    }
    
    ll total_winnings = 0;
    
    for (int j = 0; j < m; j++) {
        sort(cols[j].begin(), cols[j].end());
        
        ll current_col_sum = 0;
        for (int i = 0; i < n; i++) {
            // Formula: val * (number of elements smaller - number of elements larger)
            // i elements are smaller than cols[j][i], (n - 1 - i) are larger
            ll multiplier = (ll)i - (n - 1 - i);
            current_col_sum += (ll)cols[j][i] * multiplier;
        }
        total_winnings += current_col_sum;
    }
    
    cout << total_winnings << "\n";
}

int main() {
    // Standard fast I/O
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
**Independent Dimension Processing:** When a problem involves a multi-dimensional metric where the components (like Manhattan distance or per-coordinate absolute differences) are additive and do not interact, always decompose the problem into $m$ independent 1D problems.

### 8. Key Math Learned
**Sum of All-Pairs Absolute Differences:** The sum of $|a_i - a_j|$ for all pairs in a set is efficiently calculated by sorting. Once sorted, the "contribution" of each element is determined solely by its rank (index) in the sorted list. This avoids $O(n^2)$ summation.

### 9. Identification in Future Problems
* **Keywords:** "sum of differences", "all pairs", "absolute value", "Manhattan distance".
* **Structural Triggers:** If you see a summation over all pairs $(i, j)$ of some property $f(i, j)$ where $f$ can be decomposed by coordinate, check if sorting each coordinate simplifies the expression.
* **Constraint Triggers:** $n \times m \le 3 \cdot 10^5$ suggests that an $O(nm \log n)$ or $O(nm)$ solution is required, pointing away from $O(n^2)$ pair comparisons.
