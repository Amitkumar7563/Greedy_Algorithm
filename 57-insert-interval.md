# 57. Insert Interval — Greedy / Intervals

**Sheet:** Striver A2Z DSA Sheet  
**Problem:** [LeetCode 57 — Insert Interval](https://leetcode.com/problems/insert-interval/description/)  
**Video:** Striver's DSA Explanation

## 1. Problem Understanding

Humein sorted, non-overlapping intervals diye hain aur ek `newInterval` insert karna hai.

Agar koi interval overlap karta hai, toh unhe merge karna hai. Final result bhi sorted aur non-overlapping hona chahiye.

## 2. My Original Approach

Mera initial idea tha:

- `newInterval` ko `intervals` mein insert kar doon.
- Consecutive intervals ke starting points compare karoon.
- Phir overlapping intervals ko identify karke merge karoon.

### Why this approach is not ideal

- Sirf starting points compare karne se overlap decide nahi hota. Current interval ka **end** bhi check karna zaroori hai.
- `intervals` mein loop ke andar `push_back()` karne se iteration complicate ho sakti hai.
- Insert karne ke baad sorting aur merging ki zaroorat pad sakti hai.

**Better approach:** Given intervals already sorted hain, isliye bina sorting ke ek linear scan mein answer bana sakte hain.

## 3. Core Intuition — Striver's Approach

Hum intervals ko 3 phases mein process karenge:

### Phase 1: Non-overlapping intervals before `newInterval`

Jab tak current interval ka end, `newInterval` ke start se chhota hai, use directly `result` mein add kar do.

```cpp
while (i < n && intervals[i][1] < newInterval[0]) {
    result.push_back(intervals[i]);
    i++;
}
```

**Why?** Ye intervals new interval se pehle hain, isliye merge karne ki zaroorat nahi hai.

### Phase 2: Merge overlapping intervals

Jab tak current interval ka start, merged `newInterval` ke end se chhota ya equal hai:

```cpp
while (i < n && intervals[i][0] <= newInterval[1]) {
    newInterval[0] = min(newInterval[0], intervals[i][0]);
    newInterval[1] = max(newInterval[1], intervals[i][1]);
    i++;
}
```

**Why `min()` and `max()`?**

- `min()` se merged interval ka earliest start milta hai.
- `max()` se merged interval ka farthest end milta hai.

Har overlapping interval ke saath boundaries update hoti hain.

### Phase 3: Add merged interval and remaining intervals

```cpp
result.push_back(newInterval);

while (i < n) {
    result.push_back(intervals[i]);
    i++;
}
```

Merged interval ko pehle add karenge, phir remaining intervals. Original order sorted hai, isliye dobara sorting nahi chahiye.

## 4. Complete C++ Code

```cpp
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals,
                               vector<int>& newInterval) {
        vector<vector<int>> result;
        int n = intervals.size();
        int i = 0;

        // Phase 1: Add intervals before newInterval
        while (i < n && intervals[i][1] < newInterval[0]) {
            result.push_back(intervals[i]);
            i++;
        }

        // Phase 2: Merge overlapping intervals
        while (i < n && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = min(newInterval[0], intervals[i][0]);
            newInterval[1] = max(newInterval[1], intervals[i][1]);
            i++;
        }

        result.push_back(newInterval);

        // Phase 3: Add remaining intervals
        while (i < n) {
            result.push_back(intervals[i]);
            i++;
        }

        return result;
    }
};
```

## 5. Dry Run

**Input:**

```cpp
intervals = {{1,2}, {3,5}, {6,7}, {8,10}, {12,16}};
newInterval = {4,8};
```

| Step | Action | Result |
|---|---|---|
| 1 | `[1,2]` ends before `4` | `[[1,2]]` |
| 2 | Merge `[3,5]` | `[3,8]` |
| 3 | Merge `[6,7]` | `[3,8]` |
| 4 | Merge `[8,10]` | `[3,10]` |
| 5 | Add merged interval | `[[1,2],[3,10]]` |
| 6 | Add remaining `[12,16]` | `[[1,2],[3,10],[12,16]]` |

**Output:**

```cpp
{{1,2}, {3,10}, {12,16}}
```

## 6. Important Observations

- `intervals[i][0]` = start, `intervals[i][1]` = end.
- Phase 1 checks `intervals[i][1] < newInterval[0]`.
- Phase 2 checks `intervals[i][0] <= newInterval[1]`.
- Equality is included in the merge condition, so intervals touching at an endpoint are merged.
- `newInterval` ko update karte rehna hai, kyunki har merge ke baad uski boundaries change ho sakti hain.
- `i` har processed interval ke baad increment hota hai, isliye koi interval repeat process nahi hota.
- Empty `intervals` hone par bhi code correctly `newInterval` return karta hai.

## 7. Complexity Analysis

- **Time:** `O(n)` — har existing interval at most once process hota hai.
- **Auxiliary space:** `O(1)` extra working space, excluding output.
- **Output space:** `O(n)` for the `result` vector.

## 8. Pattern to Remember

**Intervals + Sorted Input + Merge**

1. Add intervals completely before the new interval.
2. Merge all overlapping intervals.
3. Add the merged interval.
4. Append the remaining intervals.

**Key lesson:** Jab input already sorted ho, toh unnecessary sorting ya insertion avoid karke one-pass solution socho.
