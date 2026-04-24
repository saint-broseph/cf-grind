# 1850E_Binary_Search_On_Answer

**Problem Link:** [https://codeforces.com/problemset/problem/1850/E](https://codeforces.com/problemset/problem/1850/E)

### 1. Problem Summary
Given $n$ square paintings with side lengths $s_i$ and a total cardboard area $c$, find the uniform width $w$ added to all sides of every painting such that the total area of the $n$ frames (each of side $s_i + 2w$) exactly equals $c$.

### 2. Core CS/DSA Concepts
Binary Search on Answer, Monotonic Functions, Geometry.

### 3. Core Mathematical/Algorithmic Logic
The total area function $f(w) = \sum_{i=1}^{n} (s_i + 2w)^2$ is strictly increasing (monotonic) for $w \ge 0$. Since we are looking for a specific value $w$ that satisfies $f(w) = c$, and the function is monotonic, we can binary search over the possible range of $w$.

The lower bound for $w$ is $1$, and the upper bound can be estimated by the total area $c$. Since $c$ is up to $10^{18}$, $w$ can be up to $10^9$. For each mid-value in our binary search, we calculate the sum. If the sum exceeds $c$, our $w$ is too large; if it is less than $c$, our $w$ is too small.

### 4. Time & Space Complexity
* **Brute Force:** $O(C/N)$ Time / $O(1)$ Space - Incrementally checking every possible value of $w$. Since $w$ can be $10^9$, this will TLE.
* **Better (Quadratic Formula):** $O(N)$ Time / $O(1)$ Space - Expanding $\sum (s_i + 2w)^2 = c$ into $4nw^2 + (4\sum s_i)w + (\sum s_i^2 - c) = 0$. Solving for $w$ using the quadratic formula works but requires handling very large numbers (potential precision issues with `long double`).
* **Optimal (Binary Search):** $O(N \log (\text{max\_w}))$ Time / $O(1)$ Space - Binary searching for $w$ in the range $[1, 10^9]$. Each check takes $O(N)$.

### 5. Pseudocode
```text
For each test case:
    Read n, c
    Read array s
    
    Low = 1, High = 10^9
    Result = 0
    
    While Low <= High:
        Mid = Low + (High - Low) / 2
        Total_Area = 0
        
        For each side in s:
            Total_Area += (side + 2*Mid) * (side + 2*Mid)
            If Total_Area > c: Break // Optimization to prevent overflow
            
        If Total_Area == c:
            Result = Mid
            Break
        Else If Total_Area > c:
            High = Mid - 1
        Else:
            Low = Mid + 1
            
    Print Result
  ```
  
### 6. Actual C++ Code
```cpp
#include <bits/stdc++.h>
using namespace std;

typedef __int128_t int128; // Using int128 to prevent overflow during sum calculation

void solve() {
    int n;
    long long c;
    cin >> n >> c;
    vector<long long> s(n);
    for (int i = 0; i < n; i++) cin >> s[i];

    long long low = 1, high = 1e9;
    long long ans = 0;

    while (low <= high) {
        long long mid = low + (high - low) / 2;
        int128 total = 0;
        bool overflow = false;

        for (int i = 0; i < n; i++) {
            int128 side = s[i] + 2 * mid;
            total += side * side;
            if (total > c) {
                overflow = true;
                break;
            }
        }

        if (total == (int128)c) {
            ans = mid;
            break;
        } else if (overflow || total > (int128)c) {
            high = mid - 1;
        } else {
            low = mid + 1;
        }
    }
    cout << ans << "\n";
}

int main() {
    // Fast I/O
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
**Binary Search on Answer:** When the problem asks for a specific value $X$ such that a condition is met, and increasing $X$ predictably increases or decreases the output (monotonicity), binary search on the range of possible answers is the most robust strategy.

### 8. Key Math Learned
**Monotonicity and Overflow Handling:** When calculating sums of squares, values grow quadratically. Even with `long long`, $\sum (10^9)^2$ will overflow. Using `__int128` or short-circuiting the loop as soon as `current_sum > target` is essential for competitive programming correctness.

### 9. Identification in Future Problems
* **Keywords:** "uniform width", "exactly equal to $C$", "find $w$".
* **Structural Triggers:** A monotonic relationship between the input parameter and the final result.
* **Constraint Triggers:** $C$ up to $10^{18}$ and $N$ up to $2 \cdot 10^5$ suggest an $O(N \log \text{Ans})$ approach.

### GOOGLE SHEET SUMMARY
"monotonic function", "find specific value $X$ for target $Y$", "geometry with uniform increments" | Binary Search on Answer ($O(N \log 10^9)$)
