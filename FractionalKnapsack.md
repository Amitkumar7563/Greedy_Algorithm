# Fractional Knapsack — Greedy Algorithm

## Intuition
We have items with different values and weights, and a knapsack with limited capacity. We need to maximize the total value.

**Greedy idea:** Choose the item that gives the maximum value per unit of weight first.

For each item:

`ratio = value / weight`

Sort items in descending order of this ratio.

## Why is it called Greedy?
A greedy algorithm makes the **best choice available at the current step** instead of exploring every possible combination.

Here, we select the item with the highest value-to-weight ratio first because it gives us the most value for every unit of capacity used.

Since we can take fractions of items, this local choice leads to an optimal solution for Fractional Knapsack.

**Important:** This greedy strategy works for Fractional Knapsack, but not always for 0/1 Knapsack, where items cannot be divided.

## Approach
1. Store each item's value and weight together in a pair.
2. Sort items by `value / weight` in descending order using a lambda comparator.
3. Traverse the sorted items:
   - If the complete item fits, take it and subtract its weight from the remaining capacity.
   - Otherwise, take the fraction that fits, add its value, and stop.
4. Return the total value.

## C++ Code

```cpp
class Solution {
public:
    double fractionalKnapsack(vector<long long>& val,
                              vector<long long>& wt,
                              long long capacity) {
        int n = val.size();

        vector<pair<long long, long long>> arr;

        for (int i = 0; i < n; i++) {
            arr.push_back({val[i], wt[i]});
        }

        auto lambda = [&](auto a, auto b) {
            return (double)a.first / a.second >
                   (double)b.first / b.second;
        };

        sort(arr.begin(), arr.end(), lambda);

        double totval = 0;

        for (int i = 0; i < n; i++) {
            if (arr[i].second <= capacity) {
                totval += arr[i].first;
                capacity -= arr[i].second;
            } else {
                totval += ((double)arr[i].first /
                           arr[i].second) * capacity;
                break;
            }
        }

        return totval;
    }
};
```

## Important Code Details

- `pair<value, weight>`: Keeps each item's value and weight together while sorting.
- `a.first`: Item value.
- `a.second`: Item weight.
- `auto lambda`: Compares two items using their value-to-weight ratios.
- `>`: Sorts in descending ratio order.
- `sort(arr.begin(), arr.end(), lambda)`: Applies the custom comparator.
- `capacity -= arr[i].second`: Updates the remaining capacity.
- `((double)arr[i].first / arr[i].second) * capacity`: Calculates the value of the fraction taken.
- `break`: Stops because the remaining capacity has been filled.

## Example

`val = [60, 100, 120]`

`wt = [10, 20, 30]`

`capacity = 50`

| Value | Weight | Ratio |
|---:|---:|---:|
| 60 | 10 | 6 |
| 100 | 20 | 5 |
| 120 | 30 | 4 |

The items are already sorted by descending ratio.

1. Take the first item: value = 60, remaining capacity = 40.
2. Take the second item: value = 100, remaining capacity = 20.
3. Take 20/30 of the third item: added value = 120 × (20/30) = 80.

**Maximum total value = 60 + 100 + 80 = 240.**

## Complexity

- **Time:** `O(n log n)` — sorting the items.
- **Space:** `O(n)` — storing the value-weight pairs.

## Key Takeaway
**Fractional Knapsack = Greedy + Sorting by value/weight ratio.**

Take the complete item whenever possible; otherwise, take the fraction that fits.
