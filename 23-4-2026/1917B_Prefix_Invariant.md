# 1917B_Prefix_Invariant.md

**Problem Link:** [https://codeforces.com/problemset/problem/1917/B](https://codeforces.com/problemset/problem/1917/B)

### 1. Problem Summary
Find the total number of distinct strings that can be formed from a given string by repeatedly applying an operation where you erase either the first or the second character.

### 2. Core CS/DSA Concepts
Combinatorics, Strings, Hash Sets (Tracking Distinct Elements).

### 3. Core Mathematical/Algorithmic Logic
Notice the invariant: whenever you stop applying operations, the resulting string will consist of exactly one character chosen from some collapsed prefix of the original string, followed by the entirely untouched remaining suffix. 

If we define the intact suffix as starting at index $i+1$, the length of the newly formed string will be exactly $n - i + 1$. Because strings of different lengths are fundamentally distinct, we can safely count the distinct strings by grouping them by their final length without fear of cross-length duplicates.

For a fixed suffix length (which corresponds to the suffix $s[i+1 \dots n]$), the new string's first character can be *any* character that appeared in the prefix $s[1 \dots i]$. Therefore, the number of distinct strings of length $n - i + 1$ is exactly the number of *distinct* characters in the prefix $s[1 \dots i]$. To find the total number of valid strings, we simply sum the number of unique characters present in every possible prefix from $i=1$ to $n$.

### 4. Time & Space Complexity
* **Brute Force:** $O(2^N \cdot N)$ Time / $O(2^N \cdot N)$ Space - Recursively generating all possible strings by branching (deleting index 0 or 1) at each step and storing them in a hash set to count unique final states. Will TLE/MLE instantly.
* **Better:** $O(N^2)$ Time / $O(N)$ Space - Iterating over all possible prefix splits and using a nested loop to count unique characters in each prefix using a fresh hash set.
* **Optimal:** $O(N)$ Time / $O(1)$ Space - A single pass over the string using a boolean array of size 26 (constant space) to track seen characters, maintaining a running count of unique characters, and adding it to the total at each step.

### 5. Pseudocode
```text
For each testcase:
    Read n
    Read string s
    
    Initialize total_distinct_strings = 0
    Initialize boolean array seen_chars of size 26 to False
    Initialize unique_count = 0
    
    For i from 0 to n - 1:
        char c = s[i]
        If seen_chars[c] is False:
            seen_chars[c] = True
            unique_count = unique_count + 1
            
        total_distinct_strings = total_distinct_strings + unique_count
        
    Print total_distinct_strings
```

### 6. Actual C++ Code
```cpp

#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n;
    cin >> n;
    string s;
    cin >> s;
    
    long long total_distinct_strings = 0;
    int unique_count = 0;
    
    // Constant space O(1) array to track the 26 lowercase English letters
    vector<bool> seen(26, false);
    
    for (int i = 0; i < n; i++) {
        // If we haven't seen this character in the prefix yet
        if (!seen[s[i] - 'a']) {
            seen[s[i] - 'a'] = true;
            unique_count++;
        }
        // Add the number of distinct characters in the prefix s[0...i]
        // This maps exactly to the distinct valid string configurations ending with suffix s[i+1...n]
        total_distinct_strings += unique_count;
    }
    
    cout << total_distinct_strings << "\n";
}

int main() {
    // Fast I/O for competitive programming
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
**Prefix State vs. Fixed Suffix:** When problem operations only destroy or compress elements at one end of a sequence while leaving the tail untouched, isolate the "invariant" (the untouched suffix) and evaluate the "variants" (the collapsed prefix). By anchoring the string's identity to the length of the invariant suffix, you guarantee that you never double-count states across different prefix lengths.

### 8. Key Math Learned
**Combinatorial Independence of Length:** Because each choice of prefix $s[1 \dots i]$ leaves a suffix of a unique length $n-i$, a string generated from prefix $i$ can mathematically never be identical to a string generated from prefix $j$ ($i \neq j$). This property radically simplifies the counting process, allowing us to only filter out duplicates within the exact same prefix length boundary.

### 9. Identification in Future Problems
* **Keywords:** "remove first or second", "distinct strings", "number of possible final arrays/strings".
* **Structural Triggers:** Whenever you have rules that allow you to skip elements but enforce keeping at least one element to append to a fixed tail. This immediately hints that the answer is a summation of unique reachable states at the dynamic boundary.
* **Constraint Triggers:** The sum of $N \le 10^5$ combined with "distinct strings" generally implies that actually generating and inserting strings into a `std::set` will Memory Limit Exceed (MLE) or Time Limit Exceed (TLE). You must find a mathematical property to count them in $O(N)$ or $O(N \log N)$ time using a running state.
