# Job Sequencing Problem — Greedy Algorithm

**Topic:** Greedy Algorithm | **Sheet:** Striver A2Z DSA Sheet

**Practice:** [TakeUForward — Job Sequencing Problem](https://takeuforward.org/practice/dsa/job-sequencing-problem)  
**Documentation:** [Striver's Explanation](https://takeuforward.org/blogs/data-structure-and-algorithm/job-sequencing-problem)

## 1. Problem Understanding

We are given `N` jobs. Each job contains:

- `Job ID` — unique identifier.
- `Deadline` — the latest time by which the job must be completed.
- `Profit` — earned if the job is completed on or before its deadline.

**Conditions:**

- Each job takes exactly **1 unit of time**.
- Only one job can be performed in one time slot.
- We need to maximize total profit and return the number of jobs completed and the maximum profit.

## 2. Intuition

Suppose we have these jobs:

| Job | Deadline | Profit |
|---|---:|---:|
| A | 2 | 100 |
| B | 1 | 20 |

Job A offers a higher profit, so we should try to schedule it first. However, we must also ensure that jobs with earlier deadlines get a chance to execute.

**Key observation:** Sort jobs by profit in descending order, then schedule each job in the **latest available slot on or before its deadline**.

Why the latest slot?

Suppose a job has deadline `4`. If slots `1`, `2`, and `4` are available, choose slot `4`. This preserves earlier slots for jobs with tighter deadlines.

## 3. Why Do We Use Greedy?

A Greedy algorithm makes the best available choice at each step according to a chosen rule.

Our two greedy decisions are:

1. **Highest profit first:** Consider more profitable jobs before less profitable ones.
2. **Latest available slot:** Place each job as late as possible without missing its deadline.

If no valid slot is available, skip the job.

This strategy maximizes profit under the problem's assumptions because we prioritize higher-profit jobs while preserving earlier slots for jobs with tighter deadlines.

## 4. Algorithm

1. Sort jobs by decreasing profit.
2. Find the maximum deadline, capped at `N`.
3. Create a `slot` array initialized to `-1`, where `-1` means empty.
4. Traverse jobs in sorted order.
5. For each job, start from its deadline and move backward toward slot `1`.
6. If an empty slot is found:
   - Store the job ID in that slot.
   - Increment `jobCount`.
   - Add the job's profit to `totalProfit`.
7. If no slot is free, skip the job.
8. Return `{jobCount, totalProfit}`.

## 5. C++ Code

```cpp
class Solution {
public:
    vector<int> JobScheduling(vector<vector<int>>& Jobs) {
        int n = Jobs.size();

        // Custom sorting using lambda
        auto lambda = [](vector<int>& a, vector<int>& b) {
            return a[2] > b[2];
        };

        sort(Jobs.begin(), Jobs.end(), lambda);

        int maxDeadline = 0;

        for (auto& job : Jobs) {
            maxDeadline = max(maxDeadline, job[1]);
        }

        // At most n jobs can be scheduled
        maxDeadline = min(maxDeadline, n);

        // -1 means the slot is empty
        vector<int> slot(maxDeadline + 1, -1);

        int jobCount = 0;
        int totalProfit = 0;

        for (auto& job : Jobs) {
            int id = job[0];
            int deadline = min(job[1], maxDeadline);
            int profit = job[2];

            // Find the latest available slot
            for (int t = deadline; t >= 1; t--) {
                if (slot[t] == -1) {
                    slot[t] = id;
                    jobCount++;
                    totalProfit += profit;
                    break;
                }
            }
        }

        return {jobCount, totalProfit};
    }
};
```

**Note:** Each job is represented as `{id, deadline, profit}`. We cap the maximum deadline at `N` because at most `N` jobs can be scheduled.

## 6. Custom Sorting Using Lambda — Explained Separately

### A. Lambda declaration

```cpp
auto lambda = [](vector<int>& a, vector<int>& b) {
    return a[2] > b[2];
};
```

A **lambda function** is an anonymous function that can be defined directly in the code without declaring a separate named function.

- `auto` — lets the compiler automatically determine the type of the lambda variable.
- `lambda` — the variable name storing the lambda function.
- `[]` — the capture list. It is empty because we do not need outside variables.
- `vector<int>& a` and `vector<int>& b` — two jobs passed by reference, avoiding unnecessary copies.
- `return a[2] > b[2];` — compares the profits of the two jobs.

Since each job is represented as `{id, deadline, profit}`, index `2` contains the profit.

### B. How does `auto` work?

A lambda has its own unique compiler-generated type. Instead of writing that complicated type, we use `auto`.

```cpp
auto lambda = [](int a, int b) {
    return a < b;
};
```

Here, `lambda` stores a function that compares two integers.

We can then pass it to `sort()`:

```cpp
sort(nums.begin(), nums.end(), lambda);
```

### C. How does the comparison work?

Suppose:

```cpp
vector<int> a = {1, 4, 20};
vector<int> b = {2, 2, 80};
```

The lambda evaluates:

```cpp
return a[2] > b[2];
// 20 > 80 => false
```

The result is `false`, meaning `a` should not come before `b` according to the comparison rule. The higher-profit job should be ordered first.

If the comparison returns `true`, `a` should come before `b`.

**Important:** `sort()` decides which pairs to compare. We define the comparison rule; `sort()` handles the ordering.

### D. Ascending vs. descending order

**Ascending order — smallest to largest:**

```cpp
auto lambda = [](int a, int b) {
    return a < b;
};

sort(nums.begin(), nums.end(), lambda);
```

Example: `{5, 2, 8, 1}` becomes `{1, 2, 5, 8}`.

**Descending order — largest to smallest:**

```cpp
auto lambda = [](int a, int b) {
    return a > b;
};

sort(nums.begin(), nums.end(), lambda);
```

Example: `{5, 2, 8, 1}` becomes `{8, 5, 2, 1}`.

For Job Sequencing, we use:

```cpp
auto lambda = [](vector<int>& a, vector<int>& b) {
    return a[2] > b[2];
};

sort(Jobs.begin(), Jobs.end(), lambda);
```

This sorts jobs by profit in descending order.

**Remember:** The lambda defines the comparison rule; `sort()` performs the sorting. Use `>` rather than `>=` so that the comparator follows strict ordering rules when profits are equal.

## 7. Dry Run

Consider these jobs:

```text
Jobs = {
    {1, 4, 20},
    {2, 1, 10},
    {3, 1, 40},
    {4, 1, 30}
}
```

After sorting by profit:

| ID | Deadline | Profit |
|---|---:|---:|
| 3 | 1 | 40 |
| 4 | 1 | 30 |
| 1 | 4 | 20 |
| 2 | 1 | 10 |

The maximum deadline is `4`.

Initially, all slots are empty:

```text
Slot:    1    2    3    4
        [-1] [-1] [-1] [-1]
```

**Job 3:** Deadline `1`, profit `40`.

Slot `1` is free, so schedule job `3`.

```text
[3] [-1] [-1] [-1]
```

Job count = `1`, total profit = `40`.

**Job 4:** Deadline `1`, profit `30`.

Slot `1` is occupied, and no earlier slot exists. Skip this job.

**Job 1:** Deadline `4`, profit `20`.

Slot `4` is free, so schedule job `1`.

```text
[3] [-1] [-1] [1]
```

Job count = `2`, total profit = `60`.

**Job 2:** Deadline `1`, profit `10`.

Slot `1` is occupied. Skip this job.

**Final answer:**

```text
Job count = 2
Maximum profit = 60
```

## 8. Why Do We Iterate Backward?

```cpp
for (int t = deadline; t >= 1; t--)
```

A job with deadline `4` can be scheduled in slots `1`, `2`, `3`, or `4`.

We search from the latest valid slot toward the earliest. Choosing the latest free slot preserves earlier slots for jobs that cannot be scheduled later.

This is the second important part of the greedy strategy.

## 9. Complexity Analysis

Let `N` be the number of jobs and `D` be the maximum relevant deadline, capped at `N`.

- **Sorting:** \(O(N\log N)\)
- **Slot searching:** \(O(ND)\) in the worst case
- **Overall time:** \(O(N\log N+ND)\)
- **Worst-case time:** \(O(N^2)\)
- **Auxiliary space:** \(O(D)\) for the slot array, excluding sorting-related stack space

An optimized approach using Disjoint Set Union (DSU) can find available slots more efficiently, but the backward scan is the standard introductory greedy approach.

## 10. Important Points to Remember

- Sort by **profit**, not by deadline.
- Keep each job's ID, deadline, and profit together while sorting.
- Schedule a job in the latest free slot at or before its deadline.
- A slot can contain only one job.
- If no valid slot is free, skip the job.
- Use `break` after scheduling a job because each job can be scheduled only once.
- Slot indexing is 1-based, so index `0` is unused.
- The objective is maximum profit, not necessarily maximum number of jobs.
- When profits are equal, their relative order does not affect the comparator's rule.

## Revision in One Line

**Sort by descending profit → find the latest free slot before the deadline → schedule if possible → update job count and total profit.**
