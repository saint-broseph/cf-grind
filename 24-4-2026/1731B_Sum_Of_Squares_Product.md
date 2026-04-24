# 1731B_Sum_of_Squares_Product.md

**Problem Link:** [https://codeforces.com/problemset/problem/1731/B](https://codeforces.com/problemset/problem/1731/B)

### 1. Problem Summary
Find the maximum number of killjoy dolls Kilani can collect by moving from $(1,1)$ to $(n,n)$ in an $n \times n$ grid, where the number of dolls in cell $(i,j)$ is $i \cdot j$, then multiply the result by 2022 and output it modulo $10^9 + 7$.

### 2. Core CS/DSA Concepts
Greedy Pathfinding, Combinatorics, Modular Arithmetic, Modular Inverse.

### 3. Core Mathematical/Algorithmic Logic
To maximize the sum in a grid where cell values are $i \cdot j$, the greedy choice is to stay as close to the "high-value" cells as possible. Since $i \cdot j$ increases as both $i$ and $j$ increase, the optimal path is a "staircase" along the main diagonal: $(1,1) \to (1,2) \to (2,2) \to (2,3) \to \dots \to (n,n)$.

The sum of this path is:
$S = (1 \cdot 1) + (1 \cdot 2) + (2 \cdot 2) + (2 \cdot 3) + (3 \cdot 3) + \dots + (n \cdot n)$
This can be split into two series:
1. Squares: $\sum_{i=1}^{n} i^2$
2. Products of adjacent integers: $\sum_{i=1}^{n-1} i(i+1)$

Using standard summation formulas:
$\sum_{i=1}^{n} i^2 = \frac{n(n+1)(2n+1)}{6}$
$\sum_{i=1}^{n-1} i(i+1) = \frac{(n-1)n(n+1)}{3}$

Total Sum $S = \frac{n(n+1)(2n+1)}{6} + \frac{n(n+1)(n-1)}{3} = \frac{n(n+1)(4n-1)}{6}$

Finally, the answer is $(S \cdot 2022) \pmod{10^9+7}$. Note that $2022/6 = 337$, so the expression simplifies to $n(n+1)(4n-1) \cdot 337 \pmod{10^9+7}$, removing the need for a modular inverse.

### 4. Time & Space Complexity
* **Brute Force:** $O(N)$ Time / $O(1)$ Space - Iterating through the path and summing values. This will TLE for $N=10^9$.
* **Optimal:** $O(1)$ Time / $O(1)$ Space - Using the closed-form mathematical formula.

### 5. Pseudocode
```text
For each test case:
    Read n
    // Use the derived formula: ans = (n * (n + 1) * (4n - 1) * 337) % MOD
    Term1 = n % MOD
    Term2 = (n + 1) % MOD
    Term3 = (4 * n - 1) % MOD
    
    Result = (Term1 * Term2) % MOD
    Result = (Result * Term3) % MOD
    Result = (Result * 337) % MOD
    
    Print Result
```

### 6. Actual C++ code
```cpp
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const ll MOD = 1e9 + 7;

void solve() {
    ll n;
    cin >> n;
    
    // Formula derived: [n(n+1)(4n-1)/6] * 2022
    // 2022 / 6 = 337
    // Final expression: n * (n+1) * (4n-1) * 337 % MOD
    
    ll term1 = n % MOD;
    ll term2 = (n + 1) % MOD;
    ll term3 = (4 * n - 1) % MOD;
    
    ll ans = (term1 * term2) % MOD;
    ans = (ans * term3) % MOD;
    ans = (ans * 337) % MOD;
    
    cout << ans << "\n";
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
**Formula Reduction for Large N:** When $N$ is up to $10^9$ and the problem involves a path sum or sequence sum, the solution is almost always a closed-form polynomial. Decompose the sum into standard power sums ($\sum i, \sum i^2, \sum i^3$) to find the $O(1)$ solution.

### 8. Key Math Learned
**Summation of Product Series:** The sum of products of consecutive integers $\sum_{i=1}^n i(i+1) = \frac{n(n+1)(n+2)}{3}$. Recognizing these telescopic-like identities allows for rapid formula simplification in competitive rounds.

### 9. Identification in Future Problems
* **Keywords:** "maximize sum", "grid path $(1,1)$ to $(n,n)$", "$n$ up to $10^9$".
* **Structural Triggers:** A grid value $f(i,j)$ that is monotonically increasing in both $i$ and $j$ suggests a diagonal-hugging greedy path.
* **Constraint Triggers:** $N=10^9$ strictly forbids any $O(N)$ loops, mandating $O(1)$ or $O(\log N)$ math.

### GOOGLE SHEET SUMMARY
"grid path sum maximization", "cell value $i \cdot j$", "$N=10^9$ constraint" | Mathematical Formula Derivation + Modular Arithmetic ($O(1)$)
