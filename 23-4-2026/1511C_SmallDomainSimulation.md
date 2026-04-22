# 1511C_Small_Domain_Simulation.md

**Problem Link:** [https://codeforces.com/problemset/problem/1511/C](https://codeforces.com/problemset/problem/1511/C)

### 1. Problem Summary
Given a deck of $n$ cards where each card has a color from 1 to 50, repeatedly find the position of the first card of a queried color, print its position, and move that card to the top of the deck.

### 2. Core CS/DSA Concepts
Array Manipulation, Small Constraint Optimization, State Compression (Tracking Types vs. Tracking Elements).

### 3. Core Mathematical/Algorithmic Logic
The deck has up to $3 \cdot 10^5$ cards, making a direct simulation of removing and inserting cards $O(N \cdot Q)$ which will instantly Time Limit Exceed (TLE). 

However, the crucial constraint is that there are **only 50 distinct colors**. If a query asks for the first occurrence of color $C$, we don't care about the other $300,000$ cards; we only care about where the 50 colors currently sit relative to the top.

We can maintain an array `pos[51]` that stores the 1-indexed position of the *first* occurrence of each color. 
1. When queried for color $C$, the answer is simply `pos[C]`.
2. When color $C$ is moved to the top, its new position becomes $1$ (`pos[C] = 1`).
3. For all other colors $c \neq C$, if their first occurrence was *above* the pulled card (`pos[c] < original_pos_of_C`), they are shifted down by one spot (`pos[c]++`). If they were *below* the pulled card, their position is completely unaffected.

### 4. Time & Space Complexity
* **Brute Force:** $O(N \cdot Q)$ Time / $O(N)$ Space - Simulating the deck using `std::vector::erase` and `std::vector::insert` for every query.
* **Better:** $O((N + Q) \log N)$ Time / $O(N)$ Space - Using a Fenwick Tree (Binary Indexed Tree) or Treap to manage dynamic shifts and positions. Overkill but passes.
* **Optimal:** $O(N + Q \cdot 50)$ Time / $O(50)$ Space - Using an array of size 51 to track only the first occurrence of each color. Processing a query takes exactly 50 operations. 

### 5. Pseudocode
```text
For each testcase:
    Read n, q
    Initialize array pos of size 51 to infinity (or 0)
    
    // Process initial deck state
    For i from 1 to n:
        Read color
        If pos[color] is not set:
            pos[color] = i
            
    // Process queries
    For each query_color in q:
        ans = pos[query_color]
        Print ans
        
        // Shift colors that were above the queried color
        For c from 1 to 50:
            If pos[c] < ans:
                pos[c] = pos[c] + 1
                
        // Move queried color to the top
        pos[query_color] = 1
```

### 6. Actual C++ Code
```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n, q;
    cin >> n >> q;
    
    // Use 1-based indexing for up to 50 colors
    vector<int> pos(51, 0);
    
    for (int i = 1; i <= n; i++) {
        int color;
        cin >> color;
        // Only record the *first* occurrence of each color
        if (pos[color] == 0) {
            pos[color] = i;
        }
    }
    
    for (int i = 0; i < q; i++) {
        int t;
        cin >> t;
        
        int current_pos = pos[t];
        cout << current_pos << " ";
        
        // Update the positions of all colors that were above the queried card
        for (int c = 1; c <= 50; c++) {
            // If color 'c' exists and was sitting above the card we just pulled
            if (pos[c] != 0 && pos[c] < current_pos) {
                pos[c]++;
            }
        }
        
        // Move the queried card to the very top
        pos[t] = 1;
    }
    cout << "\n";
}

int main() {
    // Standard fast I/O
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    
    // Problem has single test case based on input format
    solve();
    
    return 0;
}
```

### 7. The Key Pattern Learned
**Constraint-Driven State Compression:** Whenever a problem features a massive sequence (e.g., $N = 10^5$) but a tiny domain of values (e.g., $V \le 50$), stop tracking the sequence elements and start tracking the *domain properties*. In "move to front" or permutation problems, tracking the index of the tiny domain values completely bypasses the need for advanced Data Structures like Segment Trees.

### 8. Key Math Learned
**Relative Order Shifting:** When an element is extracted from an array and pushed to the front, the structural math of the array dictates that only elements with an index *less* than the extracted element's index are incremented by 1. Elements with an index *greater* than the extracted element remain entirely unchanged.

### 9. Identification in Future Problems
* **Keywords:** "move to front", "find first occurrence", "deck of cards".
* **Structural Triggers:** Finding items and shifting the rest of the array.
* **Constraint Triggers:** A massive mismatch between $N$ (the number of items) and $A_i$ (the max value of the items). If $A_i \le 50$ or $A_i \le 100$, it is a screaming siren that an $O(Q \cdot A_i)$ simulation is the intended path.
