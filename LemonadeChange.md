# 860. Lemonade Change

- **Difficulty:** Easy
- **Topic:** Greedy, Array, Simulation
- **LeetCode Link:** [Lemonade Change](https://leetcode.com/problems/lemonade-change/)

---

## 📌 Problem Overview

Each lemonade costs **$5**. Customers pay using either a **$5**, **$10**, or **$20** bill, in the exact sequence they arrive. You start with zero change.

Return `true` if you can give every customer correct change; otherwise, return `false`.

---

## 💡 Intuition & Greedy Strategy

At each transaction, the change needed depends entirely on the incoming bill:

| Bill Received | Change Needed | Allowed Cash Combinations |
|:---:|:---:|---|
| **$5** | $0 | None needed; keep the bill. |
| **$10** | $5 | Must give back **1 × $5**. |
| **$20** | $15 | Option A: **1 × $10 + 1 × $5**<br>Option B: **3 × $5** |

### Why is this called "Greedy"?
A **Greedy Algorithm** always picks the locally optimal choice at each step without backtracking or looking ahead, betting that local choices lead to a globally optimal solution.

### Why does Greedy work here?
The fork in logic occurs exclusively when giving change for a **$20** bill.

- **Versatility of $5 vs $10:**
  - A **$5 bill** can satisfy both future $10 customers and future $20 customers.
  - A **$10 bill** can **only** satisfy future $20 customers.
- Since **$5 bills have strictly higher future utility**, the greedy decision is to preserve as many $5 bills as possible.
- When making $15 change:
  - **Greedy choice:** Give `1 × $10 + 1 × $5` first.
  - **Fallback choice:** Give `3 × $5` only if no $10 bills exist.

---

## 🔑 Key Details & Edge Cases

1. **No need to count $20 bills:** The maximum change needed is $15, so a $20 bill is never handed back out.
2. **Immediate fail-fast:** Return `false` as soon as a required denomination cannot be supplied.

---

## 💻 C++ Implementation

```cpp
class Solution {
public:
    bool lemonadeChange(vector<int>& bills) {
        int five = 0;
        int ten = 0;

        for (int bill : bills) {
            if (bill == 5) {
                five++;
            } 
            else if (bill == 10) {
                if (five == 0) return false;
                five--;
                ten++;
            } 
            else { // bill == 20
                // Greedy choice: prioritize shedding the less versatile $10 bill
                if (ten > 0 && five > 0) {
                    ten--;
                    five--;
                } else if (five >= 3) {
                    five -= 3;
                } else {
                    return false;
                }
            }
        }

        return true;
    }
};
