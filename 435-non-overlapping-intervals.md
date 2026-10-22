# 435. Non-Overlapping Intervals — Greedy

**Topic:** Greedy Algorithms | Intervals  

## 1. Problem Understanding

Humein intervals diye hain. Minimum intervals remove karne hain taaki remaining intervals overlap na karein.

**Goal:** Minimum number of removals.

## 2. My Approach

Mera idea tha:

- Intervals ko lambda comparator se sort karna.
- `lastEndTime` maintain karna.
- Current interval ke start ko `lastEndTime` se compare karna.
- `cnt` se answer calculate karna.

Mera sorting approach correct tha, lekin counting logic mein mistake thi.

### Mistake in My Code

```cpp
if (intervals[i][0] >= lastEndTime) {
    cnt++;
    lastEndTime = intervals[i][1];
}

return n - cnt;
```

Is logic mein `cnt` non-overlapping intervals ko count kar raha tha, lekin first selected interval count nahi hua. Saath hi, `n - cnt` tabhi correct hai jab `cnt` total selected intervals ko represent kare.

## 3. Core Intuition — Greedy

**Jab intervals overlap karte hain, toh earlier ending interval ko keep karna better hai.**

Reason: Jo interval jaldi finish hota hai, woh future intervals ke liye zyada space chhodta hai.

Example:

```cpp
intervals = {{1,4}, {2,3}, {3,5}};
```

Ending time ke basis par sorted intervals:

```cpp
{{2,3}, {1,4}, {3,5}}
```

- `[2,3]` ko keep karo.
- `[1,4]` overlap karta hai, toh remove karo.
- `[3,5]` ko keep karo, kyunki `3 >= 3`.

**Minimum removals = 1**

## 4. Striver's Approach

### Step 1: Sort by ending time

```cpp
auto lambda = [](const vector<int>& a, const vector<int>& b) {
    return a[1] < b[1];
};

sort(intervals.begin(), intervals.end(), lambda);
```

Earliest ending interval ko pehle select karenge.

### Step 2: Initialize

```cpp
int cnt = 1;
int lastEndTime = intervals[0][1];
```

- `cnt = 1`: First interval already selected hai.
- `lastEndTime`: Last selected interval ka ending time.

### Step 3: Traverse and select

```cpp
for (int i = 1; i < n; i++) {
    if (intervals[i][0] >= lastEndTime) {
        cnt++;
        lastEndTime = intervals[i][1];
    }
}
```

Agar current interval ka start `lastEndTime` se greater than ya equal hai, toh interval select kar lo.

Agar overlap karta hai, toh use skip kar do. Sorting by end time ensure karti hai ki earlier-ending interval already selected hai.

### Step 4: Calculate removals

```cpp
return n - cnt;
```

Yahan `cnt` maximum non-overlapping intervals ko count karta hai.

**Formula:**

`Minimum removals = Total intervals - Maximum intervals kept`

## 5. Complete C++ Code

```cpp
class Solution {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        int n = intervals.size();

        if (n == 0) return 0;

        auto lambda = [](const vector<int>& a, const vector<int>& b) {
            return a[1] < b[1];
        };

        sort(intervals.begin(), intervals.end(), lambda);

        int cnt = 1;
        int lastEndTime = intervals[0][1];

        for (int i = 1; i < n; i++) {
            if (intervals[i][0] >= lastEndTime) {
                cnt++;
                lastEndTime = intervals[i][1];
            }
        }

        return n - cnt;
    }
};
```

## 6. Important Observations

- Sort by **ending time**, not starting time.
- `cnt = 1` because the first interval is already selected.
- `start >= lastEndTime` means no overlap.
- `start < lastEndTime` means overlap; skip the current interval.
- Equal endpoints are allowed: `[1,2]` and `[2,3]` do not overlap in this problem.

## 7. Complexity Analysis

- **Time:** `O(n log n)` — sorting dominates the linear traversal.
- **Auxiliary space:** `O(1)` excluding sorting implementation details.

## 8. Connection with N Meetings in One Room

Both problems use the same greedy strategy:

1. Sort by ending time.
2. Select the earliest-ending compatible interval.
3. Continue if `start >= lastEndTime`.

Difference:

- **N Meetings:** Return the number of meetings selected.
- **Non-Overlapping Intervals:** Return `n - cnt` to get the minimum removals.

**Key takeaway:** Maximum non-overlapping intervals select karo; baaki remove karne padenge.
