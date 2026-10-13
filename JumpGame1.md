# Greedy_Algorithm
A standard question on greedy algorithm .....
# 55. Jump Game — Greedy

## 1. Problem
We are given an array `nums`. At index `i`, `nums[i]` represents the **maximum jump length**.

Return `true` if we can reach the last index; otherwise, return `false`.

## 2. Intuition
Instead of trying every possible jump, track the **farthest index we can reach**.

Imagine:

`nums = [2, 3, 1, 1, 4]`

- Index `0` can reach up to index `2`.
- Index `1` is reachable and can extend our reach to index `4`.
- Index `4` is the last index, so the answer is `true`.

We don't need to remember the exact path. We only need to know how far we can reach.

**Core idea:** Don't focus on which jump to take; focus on the farthest reachable index.

## 3. Why Greedy?
Greedy works because we only need to check whether the destination is reachable, not find the actual path or minimum number of jumps.

At every reachable index, we calculate its potential reach and keep the maximum.

- If a position extends our reach, update it.
- If it doesn't, keep the previous maximum.
- If we encounter an index beyond our maximum reach, we are stuck.

This avoids exploring every possible jump combination.

## 4. Approach
1. Initialize `maxIndex = 0`, because we start at index `0`.
2. Traverse the array from left to right.
3. If `i > maxIndex`, return `false` because index `i` is unreachable.
4. Otherwise, update:

   `maxIndex = max(maxIndex, i + nums[i])`

5. If the loop finishes, return `true`.

## 5. C++ Code

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        int maxIndex = 0;

        for (int i = 0; i < n; i++) {
            if (i > maxIndex)
                return false;

            maxIndex = max(maxIndex, i + nums[i]);
        }

        return true;
    }
};
```

## 6. Important Code Details

### `int maxIndex = 0;`
Stores the farthest index reachable so far.

### `if (i > maxIndex) return false;`
Checks whether the current index is reachable.

If `i > maxIndex`, we cannot reach this position from any previously reachable position. Therefore, we cannot reach the destination.

**Important:** Check reachability before updating `maxIndex`. Otherwise, an unreachable index could incorrectly extend our reach.

### `i + nums[i]`
The farthest index we could reach by jumping from index `i`.

For example, if `i = 2` and `nums[i] = 4`, the potential reach is `2 + 4 = 6`.

### `max(maxIndex, i + nums[i])`
Keeps whichever is larger:
- The farthest index we could already reach.
- The farthest index reachable from the current position.

We never reduce our maximum reach.

## 7. Dry Run

Example A: `nums = [2, 3, 1, 1, 4]`

| `i` | `nums[i]` | `maxIndex` before | `i > maxIndex`? | `maxIndex` after |
|---:|---:|---:|:---:|---:|
| 0 | 2 | 0 | No | 2 |
| 1 | 3 | 2 | No | 4 |
| 2 | 1 | 4 | No | 4 |
| 3 | 1 | 4 | No | 4 |
| 4 | 4 | 4 | No | 8 |

The loop finishes, so the answer is `true`.

Example B: `nums = [3, 2, 1, 0, 4]`

| `i` | `nums[i]` | `maxIndex` before | Result |
|---:|---:|---:|---|
| 0 | 3 | 0 | Reach becomes 3 |
| 1 | 2 | 3 | Reach remains 3 |
| 2 | 1 | 3 | Reach remains 3 |
| 3 | 0 | 3 | Reach remains 3 |
| 4 | 4 | 3 | `4 > 3`, return `false` |

Index `4` is unreachable. Even though `nums[4] = 4`, we cannot use it because we cannot get to index `4`.

## 8. Edge Cases
- `[0]` → `true`: the starting index is already the last index.
- `[0, 1]` → `false`: we cannot move from index `0`.
- `[2, 0, 0]` → `true`: we can jump directly to the last index.
- `[3, 2, 1, 0, 4]` → `false`: the reachable boundary never passes index `3`.

## 9. Complexity
- **Time:** `O(n)` — each element is processed once.
- **Space:** `O(1)` — only a constant number of variables are used.

## 10. Revision Notes
- Pattern: Greedy / Reachability.
- Maintain the farthest reachable index.
- Check `i > maxIndex` before updating.
- Update using `max(maxIndex, i + nums[i])`.
- Greedy means maximizing the reachable boundary, **not necessarily taking the biggest jump**.
- Related problem: LeetCode 45 — Jump Game II. It asks for the minimum number of jumps, so its greedy logic is different.
