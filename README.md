Data Structures & Algorithms
===

**Table of Contents**

- [1. String](#1-string)
  - [1.1. KMP](#11-knuth-morris-pratt-kmp)
- [2. Advanced Data Structures](#advanced-data-structures)
  - [2.1. Suffix Array](#suffix-array)
    - [2.1.1. Build](#211-build)

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

## 2. Advanced Data Structures
### 2.1. Suffix Array
#### 2.1.1. Build

Pseudocode
```
Let A be a string of length, n.
Let P be 2D array of length, log2(n) x n.

n ← length(A)
for i ← 0, n - 1
    P(0, i) ← rank of character at i in A

cnt ← 1
for k ← 1, log2(n)
    for i ← 0, n - 1
        // tuple of (previous rank, current rank, index)
        L(i) ← (P(k - 1, i), P(k - 1, i + cnt), i)
    sort L by previous rank and current rank
    for i ← 0, n - 1
        P(k, i) ← compute new rank based on L(i) and L(i - 1)
    cnt ← 2 * cnt

Suffix Array is the inverse of P(k - 1).
```

```c++
struct SuffixNode {
    int rank[2];
    int p;
};

vector<int> buildSA(string& A) {
    int n = A.length(), m = ceil(log2(n));
    // 1 more pass for L[i].rank[1] = -1 for all i
    vector<vector<int>> P(m + 2, vector<int>(n));

    for (int i = 0; i < n; i++) {
        P[0][i] = A[i] - 'a';
    }

    int cnt, k;
    SuffixNode L[n];
    auto cmp = [](const SuffixNode& a, const SuffixNode& b) {
        return a.rank[0] == b.rank[0]
        ? (a.rank[1] < b.rank[1] ? 1 : 0)
        : (a.rank[0] < b.rank[0] ? 1 : 0);
    };

    vector<int> SA(n);
    for (cnt = 1, k = 1; cnt >> 1 < n; k++, cnt <<= 1) {
        for (int i = 0; i < n; i++) {
            L[i].rank[0] = P[k - 1][i];
            L[i].rank[1] = i + cnt < n ? P[k - 1][i + cnt] : -1;
            L[i].p = i;
        }
        sort(L, L + n, cmp);
        for (int i = 0; i < n; i++) {
            P[k][L[i].p] = i > 0 && L[i].rank[0] == L[i - 1].rank[0] && L[i].rank[1] == L[i - 1].rank[1]
                ? P[k][L[i - 1].p]
                : i;
        }
    }
    for (int i = 0; i < n; i++) {
        SA[P[k - 1][i]] = i;
    }

    return SA;
}
```