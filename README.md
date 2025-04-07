Data Structures & Algorithms
===

**Table of Contents**

- [1. String](#1-string)
  - [1.1. KMP](#11-knuth-morris-pratt-kmp)

## 1. String
### 1.1. Knuth-Morris-Pratt (KMP)

Pseudocode
```
Let S be text string.
Let P be pattern string.

// LPS of aabaaab is [0,1,0,1,2,2,3]
// If pattern does not match at i=5, begin the next match at i=2
computeLPS(P):
    m ← length(P)
    lps ← init array of size m with values as 0
    len ← 0
    for i ← 1, m - 1
        if P(i) = P(len)
            lps(i) ← len + 1
            len ← len + 1, i ← i + 1
        else if len > 0
            len ← lps(len - 1)
        else
            lps(i) ← 0, i ← i + 1

KMP(S, P):
    n ← length(S)
    m ← length(P)
    lps ← computeLPS(P)
    
    j ← 0
    for i ← 0, n - 1
        if S(i) = P(j)
            i ← i + 1, j ← j + 1
            if all patterns match
                found index (i - j)
                j ← lps(j - 1)
        else if j > 0
            j ← lps(j - 1)
        else
            i ← i + 1
```

Build LPS
```c++
vector<int> buildLPS(string& pat) {
    int m = pat.size();
    int len = 0, i = 1;
    vector<int> lps(m, 0);

    while (i < m) {
        if (pat[i] == pat[len]) {
            len++;
            lps[i] = len;
            i++;
        } else {
            if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i] = 0;
                i++;
            }
        }
    }

    return lps;
}
```

KMP
```c++
vector<int> kmp(string& txt, string& pat) {
    int n = txt.size();
    int m = pat.size();

    vector<int> lps = buildLPS(pat);
    int i = 0, j = 0;

    vector<int> ans;
    while (i < n) {
        if (txt[i] == pat[j]) {
            i++;
            j++;
            if (j == m) {
                ans.push_back(i - j);
                j = lps[j - 1];
            }
        } else {
            if (j != 0) j = lps[j - 1];
            else i++;
        }
    }

    return ans;
}
```
