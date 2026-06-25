# Pre-Day 3 Diagnostic — Cold-start Python & SQL

## SQL — LeetCode Easy (customers who visited but made no transaction)

**What was automatic:** Recognizing the pattern itself — LEFT JOIN + WHERE [right table column] IS NULL
to find rows with no match. Wrote that part without hesitation.

**What required real thought (and was wrong on first attempt):**
Used `COUNT(t.visit_id)` to count the no-transaction visits per customer — got 0 for every row.

**Root cause:** After a LEFT JOIN, unmatched rows get NULL for every column pulled from the right
table (`t`), not just the one used in the WHERE filter. The WHERE clause was correctly isolating
the no-transaction rows, but `t.visit_id` is NULL in literally every one of those surviving rows —
and COUNT() always skips nulls. So COUNT(t.visit_id) had nothing to count.

**Fix:** Count a column from the left table instead — `COUNT(v.visit_id)` — since left-table columns
are never null from the join itself.

**Reusable rule:** COUNT(rightTableCol) after LEFT JOIN + IS NULL filter → always counts nulls →
always 0. Count a left-table column, or COUNT(*), instead.

**Bonus — counting nulls directly:** COUNT() can't count nulls (it's built to skip them). To get an
actual null count: `COUNT(*) - COUNT(column)`, or `SUM(column IS NULL)` in MySQL specifically
(boolean evaluates to 1/0), or `SUM(CASE WHEN column IS NULL THEN 1 ELSE 0 END)` for portability
across dialects.

## Python — LeetCode Easy (Two Sum)

**What was automatic:** The algorithmic idea — nested loop, check every pair, look for the
target sum. Logic was right on the first attempt.

**What required real thought (lots of syntax errors, no logic errors):**
- `for i in len(nums):` — forgot `range()`. `len(nums)` is just an int; you can't loop "in" a number.
- `if nums[i] + nums[j] = target` — used `=` (assignment) instead of `==` (comparison).
- `then output[0] == i && output[1] == j` — mixed in syntax from other languages: `then` isn't
  Python at all, `&&` should be `and`, and `==` here should've been actual assignment/append, not
  comparison.
- `return as output` — not valid syntax; just `return output`.

**Reusable rule:** The errors were all syntax-level, carried over from Java/C-style habits (`&&`,
`then`, `for x in count`) — not gaps in understanding the algorithm. Worth tracking separately:
"syntax fluency" and "algorithmic thinking" are different muscles, and today showed the second is
intact while the first needs reps.

**Clean solution arrived at:**
```python
class Solution(object):
    def twoSum(self, nums, target):
        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [i, j]
        return []
```

## Overall takeaway

Both problems: pattern recognition and algorithmic logic were instant in both SQL and Python.
The actual friction was entirely in execution detail — which specific column to aggregate after
a join, and basic syntax correctness after time away from daily coding. Good signal to re-test
this same pair of problems in Week 1 and see if both gaps have closed.
