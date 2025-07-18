## B. Runner and Customer Experience

### 1. How many runners signed up for each 1 week period? (i.e. week starts `2021-01-01`)

#### Approach 1: DATE_TRUNC with Adjustment
```sql
SELECT 
    DATE_TRUNC('week', registration_date + INTERVAL '3 days') - INTERVAL '3 days' AS week_start,
    COUNT(*) AS runners_signed_up
FROM runners
GROUP BY week_start
ORDER BY week_start;
```

#### Approach 2: Mathematical Floor Division
```sql
SELECT 
    DATE '2021-01-01' + (FLOOR(EXTRACT(days FROM registration_date - DATE '2021-01-01') / 7) * 7) AS week_start,
    COUNT(*) AS runners_signed_up
FROM runners
GROUP BY week_start
ORDER BY week_start;
```

## Detailed Explanation of Both Approaches

### The Problem We're Solving
- **Goal**: Group runners by week, but weeks must start on 2021-01-01 (Friday)
- **Challenge**: Default SQL `DATE_TRUNC('week')` starts weeks on Monday
- **2021-01-01 was a Friday**, so our custom weeks are: Fri-Thu, Fri-Thu, etc.

### Approach 1: DATE_TRUNC with Adjustment - Step by Step

#### Step 1: Understanding DATE_TRUNC('week') Default Behavior
```sql
-- Default DATE_TRUNC('week') behavior:
-- 2021-01-01 (Friday) → 2020-12-28 (Monday of that week)
-- 2021-01-02 (Saturday) → 2020-12-28 (Monday of that week)  
-- 2021-01-03 (Sunday) → 2020-12-28 (Monday of that week)
-- 2021-01-04 (Monday) → 2021-01-04 (Monday - new week starts)
```

#### Step 2: The Magic of +3 Days
```sql
registration_date + INTERVAL '3 days'
```
**Why +3 days?**
- 2021-01-01 (Friday) + 3 days = 2021-01-04 (Monday)
- This shifts our target start date (Friday) to align with Monday
- Now when we use DATE_TRUNC('week'), it will treat our Friday as if it were a Monday

#### Step 3: Apply DATE_TRUNC
```sql
DATE_TRUNC('week', registration_date + INTERVAL '3 days')
```
**What happens:**
- Original: 2021-01-01 (Friday) + 3 days = 2021-01-04 (Monday)
- DATE_TRUNC('week', 2021-01-04) = 2021-01-04 (Monday)
- Original: 2021-01-02 (Saturday) + 3 days = 2021-01-05 (Tuesday)
- DATE_TRUNC('week', 2021-01-05) = 2021-01-04 (Monday of that week)

#### Step 4: Subtract 3 Days Back
```sql
DATE_TRUNC('week', registration_date + INTERVAL '3 days') - INTERVAL '3 days'
```
**Final adjustment:**
- 2021-01-04 (Monday) - 3 days = 2021-01-01 (Friday) ✓
- This gives us our desired Friday start date

#### Visual Example:
```
Original dates:    2021-01-01 (Fri), 2021-01-02 (Sat), 2021-01-03 (Sun), 2021-01-04 (Mon)
Step 1 (+3 days):  2021-01-04 (Mon), 2021-01-05 (Tue), 2021-01-06 (Wed), 2021-01-07 (Thu)
Step 2 (TRUNC):    2021-01-04 (Mon), 2021-01-04 (Mon), 2021-01-04 (Mon), 2021-01-04 (Mon)
Step 3 (-3 days):  2021-01-01 (Fri), 2021-01-01 (Fri), 2021-01-01 (Fri), 2021-01-01 (Fri)
```

### Approach 2: Mathematical Floor Division - Step by Step

#### Step 1: Calculate Days Since Start Date
```sql
EXTRACT(days FROM registration_date - DATE '2021-01-01')
```
**What this does:**
- registration_date - DATE '2021-01-01' = interval between dates
- EXTRACT(days FROM ...) = number of days in that interval

**Examples:**
- 2021-01-01 - 2021-01-01 = 0 days
- 2021-01-02 - 2021-01-01 = 1 day
- 2021-01-08 - 2021-01-01 = 7 days
- 2021-01-15 - 2021-01-01 = 14 days

#### Step 2: Divide by 7 (Week Length)
```sql
EXTRACT(days FROM registration_date - DATE '2021-01-01') / 7
```
**Purpose:** Determine which week number each date belongs to

**Examples:**
- Day 0: 0 ÷ 7 = 0.0 (Week 0)
- Day 1: 1 ÷ 7 = 0.14... (Week 0)
- Day 6: 6 ÷ 7 = 0.85... (Week 0)
- Day 7: 7 ÷ 7 = 1.0 (Week 1)
- Day 8: 8 ÷ 7 = 1.14... (Week 1)
- Day 14: 14 ÷ 7 = 2.0 (Week 2)

#### Step 3: Apply FLOOR Function
```sql
FLOOR(EXTRACT(days FROM registration_date - DATE '2021-01-01') / 7)
```
**FLOOR rounds down to nearest integer:**
- FLOOR(0.0) = 0
- FLOOR(0.14) = 0
- FLOOR(0.85) = 0
- FLOOR(1.0) = 1
- FLOOR(1.14) = 1
- FLOOR(2.0) = 2

**This gives us the week number (0, 1, 2, 3, ...).**

#### Step 4: Multiply by 7 to Get Week Start Offset
```sql
FLOOR(EXTRACT(days FROM registration_date - DATE '2021-01-01') / 7) * 7
```
**Convert week number back to days:**
- Week 0: 0 × 7 = 0 days
- Week 1: 1 × 7 = 7 days
- Week 2: 2 × 7 = 14 days

#### Step 5: Add Back to Base Date
```sql
DATE '2021-01-01' + (FLOOR(EXTRACT(days FROM registration_date - DATE '2021-01-01') / 7) * 7)
```
**Final calculation:**
- Week 0: 2021-01-01 + 0 days = 2021-01-01
- Week 1: 2021-01-01 + 7 days = 2021-01-08
- Week 2: 2021-01-01 + 14 days = 2021-01-15

#### Complete Example Walkthrough:
```
Registration Date: 2021-01-10 (Sunday)

Step 1: 2021-01-10 - 2021-01-01 = 9 days
Step 2: 9 ÷ 7 = 1.28...
Step 3: FLOOR(1.28) = 1
Step 4: 1 × 7 = 7
Step 5: 2021-01-01 + 7 days = 2021-01-08

Result: 2021-01-10 belongs to week starting 2021-01-08
```

### Week Boundaries Created by Both Approaches:
```
Week 0: 2021-01-01 (Fri) to 2021-01-07 (Thu)
Week 1: 2021-01-08 (Fri) to 2021-01-14 (Thu)  
Week 2: 2021-01-15 (Fri) to 2021-01-21 (Thu)
Week 3: 2021-01-22 (Fri) to 2021-01-28 (Thu)
...and so on
```

### Key Differences:
- **Approach 1**: Uses built-in date functions, more readable
- **Approach 2**: Pure mathematical calculation, more explicit about the logic
- **Both produce identical results** for the week grouping

**Purpose**: Shows runner acquisition patterns over time by counting signups in weekly buckets starting from 2021-01-01. This helps understand recruitment trends and seasonal patterns.

## Notes

- The analysis uses joins between `customer_orders`, `runner_orders`, and `pizza_names` tables
- CTEs (Common Table Expressions) are used for complex logic in queries 7 and 8
- Date/time functions are used for temporal analysis in queries 9 and 10
- Conditional aggregation with CASE statements helps categorize and count different scenarios
- Week calculation ensures custom week start date (2021-01-01) rather than default calendar weeks
