**Problem Link:** [https://codeforces.com/problemset/problem/1914/C](https://codeforces.com/problemset/problem/1914/C)

### 1. Problem Summary
Maximize the total experience points from exactly $k$ actions by choosing a sequential prefix of quests to complete once, and spending all remaining actions repeatedly completing the single most lucrative unlocked quest in that prefix.

### 2. Core CS/DSA Concepts
Greedy Algorithm, Prefix Sums, Running Maximum.

### 3. Core Mathematical/Algorithmic Logic
The optimal strategy dictates that Monocarp should progress linearly up to some specific quest $x$ (spending $x$ actions to gain $a_1 + a_2 + \dots + a_x$). Once he reaches $x$, he has $k - x$ actions remaining. To maximize his score from these remaining actions, he should simply identify the highest repeat-reward ($b_i$) among all the quests he has unlocked so far ($1 \le i \le x$), and spend all $k - x$ actions on it. 

It is never optimal to repeat a quest with a lower $b_i$, nor does it make sense to skip unlocking a quest if he plans to unlock a later one. By iterating the "farthest quest reached" boundary from $1$ up to $\min(n, k)$, we can evaluate the maximum possible score for every valid prefix. Maintaining a running sum of the first-time rewards ($a$) and a running maximum of the repeat rewards ($b$) allows us to calculate the total score for each prefix length in $O(1)$ time, yielding an overall $O(N)$ solution.

### 4. Pseudocode
```text
For each test case:
    Read n, k
    Read array A of size n
    Read array B of size n
    
    Initialize max_total_exp = 0
    Initialize prefix_sum_a = 0
    Initialize max_b_seen = 0
    
    // We can at most traverse all n quests, or stop early if k < n
    limit = min(n, k) 
    
    For x from 0 to limit - 1:
        prefix_sum_a = prefix_sum_a + A[x]
        max_b_seen = max(max_b_seen, B[x])
        
        remaining_actions = k - (x + 1)
        current_exp = prefix_sum_a + (remaining_actions * max_b_seen)
        
        max_total_exp = max(max_total_exp, current_exp)
        
    Print max_total_exp
```

### 5. Actual C++ Code
```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n;
    long long k;
    cin >> n >> k;
    
    vector<long long> a(n), b(n);
    for (int i = 0; i < n; i++) cin >> a[i];
    for (int i = 0; i < n; i++) cin >> b[i];
    
    long long max_total_exp = 0;
    long long prefix_sum_a = 0;
    long long max_b_seen = 0;
    
    // We can only unlock up to min(n, k) quests
    int limit = min((long long)n, k);
    
    for (int x = 0; x < limit; x++) {
        prefix_sum_a += a[x];
        max_b_seen = max(max_b_seen, b[x]);
        
        long long remaining_actions = k - (x + 1);
        long long current_exp = prefix_sum_a + (remaining_actions * max_b_seen);
        
        max_total_exp = max(max_total_exp, current_exp);
    }
    
    cout << max_total_exp << "\n";
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

### 6. The Key Pattern Learned
**Evaluating all Prefix Boundaries:** When a problem involves unlocking items sequentially and making an optimal choice from the unlocked pool, do not try to dynamically decide whether to step forward or stay put at each index. Instead, exhaustively test every possible "stopping point" (prefix boundary) by maintaining running states (prefix sums, running minimums/maximums) to calculate the theoretical best result for each boundary in $O(1)$ time.

### 7. Key Math Learned
The optimization of the discrete function $f(x) = \sum_{i=1}^{x} a_i + (k-x) \cdot \max_{i=1}^{x} b_i$. Because $a_i$ and $b_i$ are arbitrary arrays and do not follow a strict monotonic or convex/concave mathematical curve, the function is not guaranteed to be unimodal. This implies that techniques like Ternary Search or derivative-based optimization will fail. A linear scan of the search space is mathematically mandatory. 

### 8. Identification in Future Problems
* **Keywords:** "first time vs subsequent times", "sequential unlocking", "exactly $k$ operations".
* **Structural Triggers:** Whenever you have an explore vs. exploit dilemma (spending resources to reveal new options vs. spending resources to milk the best known option), instantly think of iterating over every possible "explore" depth and assuming you "exploit" for the remainder.
* **Constraint Triggers:** $N \le 2 \cdot 10^5$ combined with $K \ge 10^5$ usually confirms that an $O(N)$ single-pass greedy evaluation using running variables is the intended optimal path. Furthermore, whenever $K$ is exceptionally large (e.g., $10^9$) but $N$ is small, it heavily signals that the optimal path involves reaching a static state and repeating an action $K-N$ times.
