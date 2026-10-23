# 56. Merge Intervals — Greedy

## 1. Core Intuition

Humein overlapping intervals ko merge karna hai aur non-overlapping intervals ko alag rakhna hai.

Example:

`[[1,3], [2,6], [8,10], [15,18]]`

Output: `[[1,6], [8,10], [15,18]]`

**Key idea:** Pehle intervals ko starting point ke according sort karo. Phir current interval ko `result` ke last interval se compare karo.

## 2. My Approach & Mistakes

Maine custom lambda comparator se sorting ki aur adjacent intervals ko compare kiya.

```cpp
auto lambda = [&](vector<int>& a, vector<int>& b) {
    return a[1] < b[1];
};
sort(intervals.begin(), intervals.end(), lambda);
```

### Mistakes in my approach

- **Wrong sorting:** Maine ending point `a[1]` ke according sort kiya. Standard approach mein starting point `a[0]` ke according sort karna chahiye.
- **Only adjacent comparison:** Maine `intervals[i]` aur `intervals[i+1]` compare kiya. Isse previously merged interval ka updated ending point track nahi ho raha tha.
- **Last interval missing:** Loop `i < n-1` tak chala, isliye last interval properly add nahi ho raha tha.
- **Incorrect result construction:** Har pair ke liye naya interval push karne se duplicates ya incorrect merged ranges aa sakte hain.

## 3. Correct Approach — Greedy

1. Intervals ko starting point ke according sort karo.
2. Agar `result` empty hai, current interval add karo.
3. Agar current interval ka start, last merged interval ke end se bada hai, toh dono overlap nahi karte. Current interval add karo.
4. Otherwise, overlap hai. Last merged interval ka end update karo using `max()`.

### C++ Implementation

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> result;

        auto lambda = [](vector<int>& a, vector<int>& b) {
            return a[0] < b[0];
        };

        sort(intervals.begin(), intervals.end(), lambda);

        for (int i = 0; i < intervals.size(); i++) {
            if (result.empty() ||
                result.back()[1] < intervals[i][0]) {
                result.push_back(intervals[i]);
            } else {
                result.back()[1] = max(
                    result.back()[1], intervals[i][1]
                );
            }
        }

        return result;
    }
};
```

## 4. Important Code Details

- `a[0] < b[0]`: Starting points ke according ascending sorting.
- `result.back()`: Result vector ka last interval.
- `result.back()[1]`: Last merged interval ka ending point.
- `result.back()[1] < intervals[i][0]`: Current interval aur last merged interval ke beech gap hai.
- `max(result.back()[1], intervals[i][1])`: Merged interval ka maximum ending point maintain karta hai.

**Why `max()`?**

Agar merged interval `[1,10]` hai aur current interval `[2,5]` hai, toh ending point `10` hi rehna chahiye, `5` nahi.

## 5. Dry Run

Input: `[[1,3], [2,6], [8,10]]`

| Current interval | Action | Result |
|---|---|---|
| `[1,3]` | Empty result, add | `[[1,3]]` |
| `[2,6]` | Overlap, merge | `[[1,6]]` |
| `[8,10]` | No overlap, add | `[[1,6],[8,10]]` |

## 6. Complexity

- **Time:** `O(n log n)` — sorting dominates.
- **Auxiliary space:** `O(log n)` for typical `std::sort` stack usage.
- **Output space:** `O(n)` for the result vector.

## 7. Key Learning

**Merge Intervals ka important pattern:** Current interval ko original previous interval se nahi, `result` ke last merged interval se compare karo.

Related problem: [Non-overlapping Intervals — LeetCode 435](https://leetcode.com/problems/non-overlapping-intervals/description/)
