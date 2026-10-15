# LeetCode 455 — Assign Cookies

**Problem:** [Assign Cookies](https://leetcode.com/problems/assign-cookies/)  
**Pattern:** Greedy + Sorting + Two Pointers

## 1. Intuition

We have two arrays:

- `g[i]` = minimum cookie size required to satisfy child `i`.
- `s[j]` = size of cookie `j`.

A child is satisfied when `s[j] >= g[i]`. Each child and each cookie can be used only once.

**Our goal:** Maximize the number of satisfied children.

### Key observation

Suppose:

```cpp
g = [1, 2, 3]
s = [1, 2, 3]
```

The child who needs size `1` should receive the cookie of size `1`, not size `3`.

Why? Because a cookie of size `3` could satisfy a child who needs `3`, but a cookie of size `1` cannot.

**Greedy idea:** Satisfy the least greedy child with the smallest available cookie that is large enough.

## 2. Why do we use Greedy?

A Greedy algorithm makes the best local choice at each step, aiming to achieve the overall optimal result.

In this problem, our local choice is:

> Give the current child the smallest cookie that can satisfy them.

Why does this make sense?

1. Smaller cookies may satisfy only children with smaller greed factors.
2. Larger cookies can satisfy both less greedy and more greedy children.
3. Saving larger cookies gives us a better chance of satisfying the remaining children.
4. Once a child is satisfied, we move to the next child and never reuse that cookie.

Sorting allows us to make these choices efficiently.

**Why is it called Greedy?**

Because we make a locally optimal choice at every step instead of trying every possible cookie-child combination.

## 3. Approach

1. Sort `g` in ascending order.
2. Sort `s` in ascending order.
3. Use two pointers:
   - `i` points to the current child.
   - `j` points to the current cookie.
4. If `s[j] >= g[i]`, the cookie satisfies the child, so increment `i`.
5. Always increment `j` because the current cookie has been considered and cannot be reused.
6. Return `i`, which is the number of satisfied children.

## 4. Dry Run

**Input:**

```cpp
g = [1, 2, 3]
s = [1, 1, 2]
```

Both arrays are already sorted.

| Step | `g[i]` | `s[j]` | Condition | Action |
|---|---:|---:|---|---|
| 1 | 1 | 1 | `1 >= 1` | Satisfy child; `i=1`, `j=1` |
| 2 | 2 | 1 | `1 < 2` | Cookie too small; `j=2` |
| 3 | 2 | 2 | `2 >= 2` | Satisfy child; `i=2`, `j=3` |

Now `j == s.size()`, so the loop stops.

**Output: `2`**

Two children are satisfied. The third child needs a cookie of size `3`, but no cookies remain.

### Pointer meaning

- `i` increases only when a child is satisfied.
- `j` increases every iteration because each cookie can be used at most once.
- Therefore, `i` itself stores the number of satisfied children.

## 5. C++ Code

```cpp
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());

        int i = 0, j = 0;

        while (i < g.size() && j < s.size()) {
            if (s[j] >= g[i]) {
                i++;
            }
            j++;
        }

        return i;
    }
};
```

## 6. Important Code Details

- `sort(g.begin(), g.end())`: Processes children from least greedy to most greedy.
- `sort(s.begin(), s.end())`: Processes cookies from smallest to largest.
- `s[j] >= g[i]`: Checks whether the cookie is large enough.
- `i++`: Counts a satisfied child by moving to the next child.
- `j++`: Moves to the next cookie whether the current cookie succeeds or fails.
- `while (i < g.size() && j < s.size())`: Stops when either all children are satisfied or all cookies have been considered.
- `return i`: Returns the total number of satisfied children, not the index of the last child.

## 7. Why Does the Greedy Strategy Work?

Consider a child with greed `2` and available cookies of sizes `2` and `5`.

Giving size `2` to this child is at least as good as giving size `5`, because both satisfy the child, but size `5` might be needed by a child with greed `5`.

Also, if a cookie is too small for the current child, it will be too small for every child after them because the greed factors are sorted. So we can safely discard that cookie.

These two observations justify the greedy decisions.

## 8. Complexity Analysis

Let `n = g.size()` and `m = s.size()`.

- **Time:** \(O(n\log n + m\log m)\) for sorting, followed by \(O(n+m)\) for the two-pointer traversal.
- **Auxiliary space:** Sorting may use implementation-dependent extra space; the traversal itself uses \(O(1)\) extra space.

## 9. Important Takeaways

- Greedy problems often involve sorting to make good local decisions.
- Two pointers help process two sorted arrays efficiently.
- Use the smallest sufficient resource first to preserve larger resources for future needs.
- A pointer can also act as a counter when it advances only after a successful match.
- Do not increment `i` when the cookie is too small.
- Do not forget that `j` must advance on every iteration.

**One-line revision:** Sort both arrays, match the smallest available cookie to the least greedy child whenever possible, and return the number of successful matches.
