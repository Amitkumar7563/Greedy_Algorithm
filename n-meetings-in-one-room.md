# N Meetings in One Room — Greedy

**Topic:** Greedy Algorithm  
**Sheet:** Striver A2Z DSA Sheet  
**Problem:** [TakeUForward — N Meetings in One Room](https://takeuforward.org/practice/dsa/n-meetings-in-one-room)

## 1. Problem Understanding

Humein do arrays diye gaye hain:

- `start[i]` → Meeting `i` ka starting time.
- `end[i]` → Meeting `i` ka ending time.

Ek room hai aur ek time par sirf ek meeting ho sakti hai.

**Goal:** Maximum kitni meetings conduct kar sakte hain, ye find karna hai.

### Example

```cpp
start = {1, 3, 0, 5, 8, 5};
end   = {2, 4, 6, 7, 9, 9};
```

**Output:** `4`

Hum ye meetings select kar sakte hain:

`(1,2) → (3,4) → (5,7) → (8,9)`

Total 4 meetings.

## 2. Core Intuition — Greedy Kyun?

Maan lo do meetings hain:

- Meeting A: `(1, 10)`
- Meeting B: `(2, 3)`

Agar hum Meeting A select karte hain, toh room time `10` tak occupied rahega.

Lekin agar Meeting B select karte hain, toh room time `3` par hi free ho jayega. Ab hum future mein aur meetings conduct kar sakte hain.

**Isliye greedy choice hai: Hamesha us meeting ko prefer karo jo sabse pehle end hoti hai.**

### Greedy ka main idea

Har step par aisa local decision lena jo future mein maximum opportunities de.

Yahan earliest ending meeting select karne se room jaldi free hota hai aur baaki meetings ke liye zyada chances milte hain.

## 3. Approach — Step by Step

1. Har meeting ka `start`, `end` aur `pos` ek saath store karo.
2. Meetings ko `end` time ke ascending order mein sort karo.
3. Pehli meeting select karo, kyunki woh sabse pehle end hoti hai.
4. `cnt = 1` rakho, kyunki ek meeting select ho chuki hai.
5. `freeTime` mein selected meeting ka ending time store karo.
6. Baaki meetings traverse karo:
   - Agar `arr[i].start > freeTime` hai, toh meeting select karo.
   - `cnt++` karo.
   - `freeTime = arr[i].end` update karo.
7. Finally `cnt` return kar do.

## 4. C++ Code — Striver's Approach

```cpp
class meeting {
public:
    int start;
    int end;
    int pos;

    meeting(int s, int e, int p) {
        start = s;
        end = e;
        pos = p;
    }
};

bool comp(meeting a, meeting b) {
    return a.end < b.end;
}

class Solution {
public:
    int maxMeetings(vector<int>& start, vector<int>& end) {
        int n = start.size();
        vector<meeting> arr;

        for (int i = 0; i < n; i++) {
            arr.push_back(
                meeting(start[i], end[i], i + 1)
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

*Note: Agar platform par class ka naam `Meeting` ya function signature alag diya hai, toh uske according adjust karna. Algorithm same rahega.*

## 5. Code Ko Chhote Parts Mein Samjho

### Part A: Meeting ka data store karna

```cpp
class meeting {
public:
    int start;
    int end;
    int pos;
};
```

Har meeting ke 3 attributes hain:

- `start` — meeting kab shuru hogi.
- `end` — meeting kab khatam hogi.
- `pos` — original meeting number.

**Teeno ko ek saath kyun rakha?**

Agar hum sirf `end` array ko sort kar dein, toh original starting aur ending times ka connection disturb ho sakta hai.

Isliye poori meeting ko ek unit ki tarah store karte hain.

### Part B: Constructor samjho

```cpp
meeting(int s, int e, int p) {
    start = s;
    end = e;
    pos = p;
}
```

Jab hum likhte hain:

```cpp
meeting(start[i], end[i], i + 1);
```

Toh constructor values ko object ke andar store kar deta hai.

Example:

```cpp
meeting(3, 4, 2);
```

Is meeting ke liye:

```cpp
start = 3;
end = 4;
pos = 2;
```

### Part C: Comparator — Sorting ka logic

```cpp
bool comp(meeting a, meeting b) {
    return a.end < b.end;
}
```

Ye function `sort()` ko batata hai ki meetings ko kis order mein rakhna hai.

- Agar `a.end < b.end` true hai, toh `a` ko `b` se pehle rakhna hai.
- Agar false hai, toh `a` ko `b` se pehle rakhne ki zaroorat nahi hai.

**Yaad rakho:** Comparator ka kaam sirf sorting order decide karna hai. Meeting select karna iska kaam nahi hai.

### Part D: Meeting objects banana

```cpp
int n = start.size();
vector<meeting> arr;

for (int i = 0; i < n; i++) {
    arr.push_back(meeting(start[i], end[i], i + 1));
}
```

- `n` → total meetings.
- `arr` → meetings store karne ke liye vector.
- `push_back()` → har meeting ko vector mein add karta hai.
- `i + 1` → meeting ki 1-based position.

### Part E: Sorting

```cpp
sort(arr.begin(), arr.end(), comp);
```

Ab meetings ending time ke ascending order mein aa jayengi.

**Core reason:** Humein pehle woh meeting consider karni hai jo room ko sabse jaldi free kare.

### Part F: Initialization

```cpp
int cnt = 1;
int freeTime = arr[0].end;
```

**`cnt = 1` kyun?**

Sorted array ki pehli meeting hum select kar chuke hain. Isliye count 1 se start hoga.

**`freeTime = arr[0].end` kyun?**

Ye batata hai ki selected meeting khatam hone ke baad room kab available hoga.

Example: Agar pehli meeting `(1,2)` hai, toh `freeTime = 2`.

### Part G: Baaki meetings check karna

```cpp
for (int i = 1; i < n; i++) {
    if (arr[i].start > freeTime) {
        cnt++;
        freeTime = arr[i].end;
    }
}
```

Loop `i = 1` se shuru hota hai kyunki pehli meeting already select ho chuki hai.

**Condition:**

```cpp
arr[i].start > freeTime
```

Check karta hai ki current meeting, last selected meeting ke khatam hone ke baad shuru ho rahi hai ya nahi.

Agar condition true hai:

```cpp
cnt++;
freeTime = arr[i].end;
```

- `cnt++` → ek aur meeting select ho gayi.
- `freeTime` update → ab room current selected meeting ke end par free hoga.

Agar condition false hai, toh meeting skip kar do. `cnt` aur `freeTime` ko change mat karo.

### Part H: Answer return karna

```cpp
return cnt;
```

Saari meetings check karne ke baad selected meetings ka count return kar do.

## 6. Dry Run

Input:

```cpp
start = {1, 3, 0, 5, 8, 5};
end   = {2, 4, 6, 7, 9, 9};
```

Sorting ke baad:

| Start | End | Decision | Reason |
|---:|---:|---|---|
| 1 | 2 | Select | Pehli meeting |
| 3 | 4 | Select | `3 > 2` |
| 0 | 6 | Skip | `0` is not greater than `4` |
| 5 | 7 | Select | `5 > 4` |
| 8 | 9 | Select | `8 > 7` |
| 5 | 9 | Skip | `5` is not greater than `9` |

Final answer:

```cpp
cnt = 4;
freeTime = 9;
```

**Output: `4`**

## 7. Important Details

### `>` kyun use kiya, `>=` kyun nahi?

Is problem ke convention ke according, agar ek meeting time `2` par end hoti hai aur doosri time `2` par start hoti hai, toh unhe compatible nahi maana jaata.

Isliye:

```cpp
arr[i].start > freeTime
```

Use karte hain.

### `pos` ka kya use hai?

`pos` original meeting number store karta hai. Agar humein sirf count return karna hai, toh `pos` ki zaroorat nahi hai.

Lekin agar selected meetings ke original indices print karne hon, toh `pos` useful hoga.

### `freeTime` har baar update kyun nahi karte?

Sirf selected meeting ke end time par room ki availability change hoti hai. Skipped meeting ko select hi nahi kiya, isliye uske end time se `freeTime` update nahi karna chahiye.

## 8. Greedy Choice Optimal Kyun Hai?

Maan lo kisi optimal solution mein pehli meeting woh nahi hai jo sabse jaldi end hoti hai.

Hum us pehli meeting ko earliest-ending meeting se replace kar sakte hain. Kyunki nayi meeting jaldi ya usi time end hogi, baad ki selected meetings ke liye room available reh sakta hai.

Is replacement se total meetings ki count kam nahi hoti.

Isi logic ko remaining meetings par repeat kar sakte hain. Isliye earliest-ending meeting choose karne wali greedy strategy optimal hai.

## 9. Complexity Analysis

- **Time Complexity:** `O(N log N)` — sorting ki wajah se.
- **Auxiliary Space:** `O(N)` — meetings store karne ke liye vector.

## 10. Common Mistakes

1. Ending time ki jagah starting time se sort karna.
2. Meeting select karne ke baad `freeTime` update karna bhool jaana.
3. Meeting skip hone par bhi `freeTime` update kar dena.
4. `>` ki jagah `>=` use karna, jab problem strict compatibility maangti hai.
5. Pehli meeting count karne ke baad loop ko `i = 0` se start karna.
6. `pos` ko necessary samajhna, jab sirf count chahiye.

## 11. Quick Revision Formula

**Sort by end time → First meeting select → Compatibility check → Count increase → `freeTime` update.**

Ek line mein intuition:

**Jo meeting sabse jaldi khatam hoti hai, use choose karo taaki room baaki meetings ke liye jaldi available ho sake.**
