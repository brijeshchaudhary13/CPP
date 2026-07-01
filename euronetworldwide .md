#euronetworldwide 
## C++ Section

### Q1. Which of the following code cannot replace `// Replace code here` to print?

```
apple ball cat dog egg
```

**Answer:** Reverse iterator (`std::set<std::string>::reverse_iterator`)

---

### Q2. Difference between

```cpp
BTree btree;
```

and

```cpp
BTree *btree = new BTree;
```

**Answer:** All of the given options

---

### Q3. Enum Question

```cpp
enum Days { Sunday, Monday=5, Tuesday, Wednesday };
```

What is the value of `Sunday`?

**Answer:** `0`

---

### Q4. Which are compile-time polymorphism?

A. Function Overloading

B. Operator Overloading

C. Virtual Functions

**Answer:** Both A and B

---

### Q5. Which statement is true?

A. Private members of base class are accessible in derived class.

B. Protected members are accessible in derived class.

**Answer:** Only B

---

### Q6. Output

```cpp
class A{
public:
    virtual int func(){ return 0; }
};

class B : public A{
    int func(){ return 1; }
};

B b;
A *a=&b;

cout<<a->func();
```

**Answer:** `1`

---

### Q7. STL List Output

```cpp
list<int> l;

for(int i=1;i<10;i++)
    l.push_back(i+i);

l.resize(5);
l.resize(7,20);
l.remove(10);

reverse print
```

**Answer**

```
20 20 8 6 4 2
```

---

### Q8.

```cpp
for(unsigned char i=1;i!=0;++i)
```

How many times does loop execute?

**Answer:** `255`

---

### Q9.

```
p->val
```

Equivalent to?

**Answer**

```cpp
(*p).val
```

---

### Q10.

`std::auto_ptr`

Output after ownership transfer

```cpp
c=a;
b=c;
```

**Answer**

```
a loc : address
a val : 10
b loc : same address
b val : 10
a loc : 0
c loc : 0
```

---

### Q11.

Which is NOT a form of polymorphism?

* Function Overloading
* Function Overriding
* Operator Overloading
* Virtual Functions

**Answer**

```
None of the given options
```

---

### Q12.

```cpp
lock_guard<mutex> lock(mute);

ReadDailyStoreTransaction();

cout<<...
```

What happens?

**Answer**

```
Lock is held until all records are read, blocking other threads.
```

---

# MySQL Section

---

## Q1.

How to select a database?

**Answer**

```sql
USE database_name;
```

---

## Q2.

Find train name and type description of trains running between **0600 and 1800**.

**Answer**

```sql
SELECT td.train_name,
       tt.type_description
FROM train_details_tbl td
JOIN train_type_tbl tt
ON td.train_type = tt.train_type
WHERE td.train_time BETWEEN '0600' AND '1800';
```

---

## Q3.

Correct UNION query

**Answer**

```sql
SELECT first_name, last_name, login, password
FROM Customer1
UNION
SELECT first_name, last_name, login, password
FROM Warehouse1
UNION
SELECT first_name, last_name, login, password
FROM Customer2
ORDER BY last_name, first_name;
```

---
# C++ Coding Question

## Problem Name

**Investment Portfolios**

---

## Problem Statement

Emma works as a financial analyst, and her supervisor has assigned her the task of analyzing **N** different investment portfolios. Each portfolio has a unique risk score.

The supervisor has asked Emma to divide these consecutive portfolios into groups such that the **total risk differential** across all groups is maximized.

The **risk differential** of a group is defined as:

> **Highest risk score − Lowest risk score**

If a group contains only **one portfolio**, its risk differential is **0**.

Your task is to determine the **maximum possible total risk differential** after dividing the portfolios into one or more **contiguous groups**.

---

## Input Specification

* **input1:** An integer **N**, representing the number of portfolios.
* **input2:** An integer array **A[]**, representing the risk scores of the portfolios.

---

## Output Specification

Return an integer representing the **maximum total risk differential** across all groups.

---

## Constraints

* Groups must contain **consecutive (contiguous)** portfolios.
* Every portfolio must belong to exactly one group.
* A group with one element contributes **0**.

---

## Example 1

### Input

```text
input1 = 5

input2 = {1, 3, 2, 4, 5}
```

### Possible Partition

```
Group 1 : {1,3}
Difference = 3 − 1 = 2

Group 2 : {2,4,5}
Difference = 5 − 2 = 3
```

Total

```
2 + 3 = 5
```

### Output

```text
5
```

---

## Example 2

### Input

```text
input1 = 1

input2 = {5}
```

### Partition

```
Group 1 : {5}
Difference = 0
```

### Output

```text
0
```

---

# Approach

This problem is solved using **Dynamic Programming**.

Let

```
dp[i]
```

be the maximum total differential considering the first **i** portfolios.

For every ending position **i**, try every possible starting position **j**.

For each group

```
A[j...i]
```

calculate

```
Difference = Maximum − Minimum
```

Then

```
dp[i] = max(dp[i],
            dp[j-1] + Difference)
```

Finally,

```
Answer = dp[N]
```

---

# Time Complexity

```
O(N²)
```

---

# Space Complexity

```
O(N)
```

---

# C++ Solution

```cpp
#include <climits>

int maximumRiskDifferential(int input1, int input2[])
{
    int n = input1;
    int dp[505];

    dp[0] = 0;

    for (int i = 1; i <= n; i++)
    {
        dp[i] = 0;

        int mn = INT_MAX;
        int mx = INT_MIN;

        for (int j = i; j >= 1; j--)
        {
            if (input2[j - 1] < mn)
                mn = input2[j - 1];

            if (input2[j - 1] > mx)
                mx = input2[j - 1];

            int diff = mx - mn;

            if (dp[j - 1] + diff > dp[i])
                dp[i] = dp[j - 1] + diff;
        }
    }

    return dp[n];
}
```

---

## Dry Run

### Input

```
5

1 3 2 4 5
```

### DP Calculation

```
dp[0] = 0

dp[1] = 0

dp[2] = 2

dp[3] = 2

dp[4] = 4

dp[5] = 5
```

### Final Answer

```
5
```

---

## Key Observation

The optimal partition is:

```
{1,3}   → 3−1 = 2
{2,4,5} → 5−2 = 3
```

Total maximum risk differential:

```
2 + 3 = 5
```

This is the optimal solution with **O(N²)** time complexity and **O(N)** auxiliary space.
