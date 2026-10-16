# Shortest Job First (SJF) — Greedy Algorithm

## 1. Intuition

We are given `bt`, where `bt[i]` is the burst time (execution time) of a process.

Our goal is to **minimize the average waiting time** of all processes.

The idea is simple: execute the process requiring the least time first.

- Short processes finish quickly.
- They contribute less to the waiting time of the remaining processes.
- Therefore, sorting burst times in ascending order minimizes the total waiting time.

This is called **Shortest Job First (SJF)**.

## 2. Why Greedy?

Greedy means making the best choice at each step.

Here, we choose the shortest available process first because delaying a short process behind a longer one increases the total waiting time.

For example, consider two processes with burst times `2` and `5`.

- Order `[2, 5]`: waiting times = `0, 2`; total = `2`.
- Order `[5, 2]`: waiting times = `0, 5`; total = `5`.

Executing the shorter process first gives a smaller total waiting time.

**Greedy choice:** Always execute the process with the smallest burst time next.

## 3. Approach

1. Sort `bt` in ascending order.
2. Initialize `time = 0` and `wtTime = 0`.
3. For each process:
   - Add `time` to `wtTime`, because `time` is the current process's waiting time.
   - Add `bt[i]` to `time`, because the process has now finished executing.
4. Return `wtTime / n` to calculate the average waiting time.

## 4. C++ Code

```cpp
class Solution {
public:
    long long solve(vector<int>& bt) {
        int n = bt.size();

        sort(bt.begin(), bt.end());

        long long time = 0;
        long long wtTime = 0;

        for (int i = 0; i < n; i++) {
            wtTime += time;
            time += bt[i];
        }

        return wtTime / n;
    }
};
```

## 5. Dry Run

Input: `bt = [4, 3, 7, 1]`

After sorting: `[1, 3, 4, 7]`

| Process | Burst time | Waiting time | Total waiting time |
|---|---:|---:|---:|
| P1 | 1 | 0 | 0 |
| P2 | 3 | 1 | 1 |
| P3 | 4 | 4 | 5 |
| P4 | 7 | 8 | 13 |

- Total waiting time = `0 + 1 + 4 + 8 = 13`
- Average waiting time = `13 / 4 = 3` (integer division)

**Answer: `3`**

## 6. Important Code Logic

```cpp
wtTime += time;
time += bt[i];
```

- `time` stores the total execution time of all previously completed processes.
- `wtTime` stores the sum of waiting times of all processed processes.
- The current process waits for all previously completed processes, so its waiting time equals `time`.

**Why update `wtTime` first?** The current process's waiting time does not include its own burst time. Therefore, we add the current `time` before increasing it by `bt[i]`.

## 7. Complexity Analysis

- **Time:** `O(n log n)` for sorting + `O(n)` for traversal = `O(n log n)`.
- **Auxiliary space:** Depends on the sorting implementation; typically `O(log n)` stack space for `std::sort`.

## 8. Important Points to Remember

- SJF is a **greedy scheduling algorithm**.
- Sorting in ascending order minimizes the total and average waiting time when all processes are available at the start.
- This solution assumes all processes arrive at time `0`.
- `time` represents elapsed execution time, not the waiting time of just one fixed process.
- Use `long long` for accumulated time and waiting-time sums to reduce the risk of integer overflow.
- In C++, integer division discards the fractional part. The expected return type and problem requirements determine whether this is appropriate.

**Revision in one line:** Sort burst times in ascending order, add elapsed time to total waiting time, then update elapsed time using the current burst time.
