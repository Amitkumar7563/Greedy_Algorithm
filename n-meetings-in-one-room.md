# N Meetings in One Room — Greedy

**Topic:** Greedy Algorithm  
**Sheet:** Striver A2Z DSA Sheet  
**Problem:** [TakeUForward — N Meetings in One Room](https://takeuforward.org/practice/dsa/n-meetings-in-one-room)  
**Video:** [Striver's Explanation](https://www.youtube.com/watch?v=II6ziNnub1Q)

## 1. Problem Understanding

We are given two arrays:

- `start[i]` → starting time of meeting `i`
- `end[i]` → ending time of meeting `i`

Only one meeting can happen at a time. We need to **select the maximum number of non-overlapping meetings** in the same room.

**Example**

```cpp
start = {1, 3, 0, 5, 8, 5};
end   = {2, 4, 6, 7, 9, 9};
```

**Output:** `4`

One optimal selection is:

`(1,2) → (3,4) → (5,7) → (8,9)`

## 2. Core Intuition — Why Greedy?

Imagine two available meetings:

- Meeting A: `(1, 10)`
- Meeting B: `(2, 3)`

If we select A, the room stays occupied until time `10`.

If we select B, the room becomes available at time `3`, leaving more opportunities for future meetings.

**Greedy rule: Always choose the compatible meeting that ends earliest.**

Why?

1. An earlier ending time frees the room sooner.
2. More time remains for other meetings.
3. We make the best local choice at each step to maximize the total number of meetings.

We do not care about the longest meeting or the earliest starting meeting. Our goal is to accommodate the **maximum count**.

## 3. Why Sort by Ending Time?

Suppose meetings are sorted by their ending times in ascending order.

The first meeting ends earliest among all available meetings. After selecting it, we look for the next meeting that starts after it ends.

Because the meetings are sorted by end time, we process promising candidates first.

**Important:** Sorting by start time or duration does not guarantee the maximum number of meetings.

## 4. Approach

1. Create a vector of meetings containing `{start, end, position}`.
2. Sort the meetings according to their ending time.
3. Select the first meeting.
4. Set `freeTime` to its ending time and initialize `cnt = 1`.
5. Traverse the remaining meetings:
   - If `arr[i].start > freeTime`, select the meeting.
   - Increase `cnt`.
   - Update `freeTime = arr[i].end`.
6. Return `cnt`.

**Why `>` and not `>=`?**

In this problem, a meeting starting exactly when the previous meeting ends is considered overlapping. Therefore, the next meeting must start strictly after `freeTime`.

## 5. C++ Code — Striver's Approach

```cpp
class Meeting {
public:
    int start;
    int end;
    int pos;

    Meeting(int s, int e, int p) {
        start = s;
        end = e;
        pos = p;
    }
};

bool comp(Meeting a, Meeting b) {
    return a.end < b.end;
}

class Solution {
public:
    int maxMeetings(vector<int>& start, vector<int>& end) {
        int n = start.size();
        vector<Meeting> arr;

        for (int i = 0; i < n; i++) {
            arr.push_back(
                Meeting(start[i], end[i], i + 1)
            );
        }

        sort(arr.begin(), arr.end(), comp);

        int cnt = 1;
        int freeTime = arr[0].end;

        for (int i = 1; i < n; i++) {
            if (arr[i].start > freeTime) {
                cnt++;
                freeTime = arr[i].end;
            }
        }

        return cnt;
    }
};
```

## 6. Code Breakdown — Understand Every Part

### Part A: Store each meeting

```cpp
arr.push_back(Meeting(start[i], end[i], i + 1));
```

Each meeting stores three values:

- `start` — when it begins.
- `end` — when it finishes.
- `pos` — its original meeting number, using 1-based indexing.

**Why store them together?**

If we sort the meetings, their start and end times must remain associated with the correct meeting.

The `pos` field is useful when we need to print the original meeting numbers in the selected order. For this problem, which asks only for the count, it is not necessary for the final answer.

### Part B: The comparator

```cpp
bool comp(Meeting a, Meeting b) {
    return a.end < b.end;
}
```

The comparator tells `sort()` how to order two meetings.

- If `a.end < b.end` is `true`, `a` should come before `b`.
- If it is `false`, `a` should not be placed before `b` on the basis of this comparison.

The result is ascending order of ending time.

**Remember:** The comparator determines the sorting order. It does not select meetings.

### Part C: Sort the meetings

```cpp
sort(arr.begin(), arr.end(), comp);
```

This arranges meetings from the smallest ending time to the largest.

Sorting establishes the order in which the greedy algorithm considers meetings.

### Part D: Initialize the answer

```cpp
int cnt = 1;
int freeTime = arr[0].end;
```

**Why `cnt = 1`?**

The first meeting in the sorted array ends earliest, so we select it. Therefore, one meeting is already selected.

**Why `freeTime = arr[0].end`?**

It records when the room becomes available after the last selected meeting.

We compare every future meeting's start time with this value.

### Part E: Traverse the remaining meetings

```cpp
for (int i = 1; i < n; i++) {
    if (arr[i].start > freeTime) {
        cnt++;
        freeTime = arr[i].end;
    }
}
```

We start at index `1` because index `0` has already been selected.

**The condition:**

```cpp
arr[i].start > freeTime
```

This checks whether the current meeting can begin without overlapping the last selected meeting.

**If the condition is true:**

```cpp
cnt++;
freeTime = arr[i].end;
```

- `cnt++` counts the newly selected meeting.
- `freeTime = arr[i].end` updates the room's next available time.

**If the condition is false:** Skip the meeting. Do not update `cnt` or `freeTime`.

### Part F: Return the answer

```cpp
return cnt;
```

After examining all meetings, return the maximum count found by the greedy strategy.

## 7. Dry Run

Input:

```cpp
start = {1, 3, 0, 5, 8, 5};
end   = {2, 4, 6, 7, 9, 9};
```

After sorting by ending time:

| Start | End | Decision | Reason |
|---:|---:|---|---|
| 1 | 2 | Select | First meeting |
| 3 | 4 | Select | `3 > 2` |
| 0 | 6 | Skip | `0` is not greater than `4` |
| 5 | 7 | Select | `5 > 4` |
| 8 | 9 | Select | `8 > 7` |
| 5 | 9 | Skip | `5` is not greater than `9` |

Final values:

```cpp
cnt = 4;
freeTime = 9;
```

**Answer:** `4`

## 8. Important Observations

- The greedy choice is based on the **ending time**, not the starting time.
- `freeTime` always represents the end time of the last selected meeting.
- Update `freeTime` only when a meeting is selected.
- The first meeting is selected because it has the earliest ending time.
- `pos` is useful for recovering the original meeting numbers but is not required when returning only the count.
- If there is one meeting, the answer is `1`.
- The given constraint ensures `N >= 1`, so initializing `cnt = 1` is valid.

## 9. Why Is This Greedy Choice Optimal?

Consider an optimal schedule whose first selected meeting is not the meeting that finishes earliest.

We can replace that first meeting with the earliest-ending meeting. Since the replacement ends no later than the original meeting, all the subsequent meetings in the optimal schedule can still fit.

The number of selected meetings does not decrease.

Therefore, an optimal solution can always begin with the earliest-ending meeting. Applying the same reasoning to the remaining meetings establishes the greedy strategy.

This is the **exchange argument** for the classic Activity Selection problem.

## 10. Complexity Analysis

Let `N` be the number of meetings.

- **Time Complexity:** `O(N log N)` for sorting, followed by an `O(N)` traversal.
- **Auxiliary Space:** `O(N)` for storing the meetings in the vector, excluding the sorting implementation's internal stack.

## 11. Common Mistakes

1. Sorting by start time instead of end time.
2. Forgetting to update `freeTime` after selecting a meeting.
3. Updating `freeTime` even when a meeting is skipped.
4. Using `>=` instead of `>` for the compatibility condition in this version of the problem.
5. Starting the loop at index `0` after already counting the first meeting.
6. Forgetting that `cnt = 1` assumes at least one meeting exists.

## 12. Pattern to Remember

**Sort → Select the first → Check compatibility → Count → Update the ending time.**

This problem is also known as **Activity Selection** and is a foundational example of the greedy algorithm.
