# SQL Data Cleaning

## Customer Orders Cleaning

### 1. Use a CTE or subquery if you just need cleaned values in your analysis:

```sql
WITH cleaned_orders AS (
  SELECT *,
    CASE 
      WHEN exclusions IS NULL OR exclusions IN ('null', '') THEN 'none'
      ELSE exclusions
    END AS cleaned_exclusions,
    CASE 
      WHEN extras IS NULL OR extras IN ('null', '') THEN 'none'
      ELSE extras
    END AS cleaned_extras
  FROM customer_orders
)
SELECT * FROM cleaned_orders;
```

**🧠 Why this is solid:**
- Keeps raw data untouched
- Reusable in analysis
- Accounts for real NULLs and string 'null'

### ✅ 2. Create a view if you'll reuse the cleaned data often:

```sql
CREATE VIEW v_cleaned_customer_orders AS
SELECT *,
  CASE 
    WHEN exclusions IS NULL OR exclusions IN ('null', '') THEN 'none'
    ELSE exclusions
  END AS cleaned_exclusions,
  CASE 
    WHEN extras IS NULL OR extras IN ('null', '') THEN 'none'
    ELSE extras
  END AS cleaned_extras
FROM customer_orders;
```

**🧠 Why this is better for recurring use:**
- Avoids rewriting cleaning logic in every query
- Keeps your queries cleaner and more focused

### ⚠️ 3. If you are allowed to modify the table (destructive action):

```sql
UPDATE customer_orders
SET exclusions = 'none'
WHERE exclusions IS NULL OR exclusions IN ('null', '');
```

```sql
UPDATE customer_orders
SET extras = 'none'
WHERE extras IS NULL OR extras IN ('null', '');
```

**🧠 Use this only when:**
- You're sure no downstream process depends on original values
- Cleaning needs to be permanent

## Method Comparison

| Use Case | Method |
|----------|--------|
| Temporary cleaning in a query | CASE in SELECT or CTE |
| Reusable across queries | Create a VIEW |
| Permanent table update (destructive) | UPDATE statement |

## Runner Orders Cleaning

```sql
WITH cleaned_runner_orders AS (
  SELECT 
    order_id,
    runner_id,
    
    -- Clean pickup_time: treat 'null' and '' as NULL
    CASE 
      WHEN pickup_time = 'null' OR pickup_time = '' THEN NULL
      ELSE pickup_time
    END AS cleaned_pickup_time,
    
    -- Clean distance: strip 'km', convert to numeric
    CASE 
      WHEN distance IS NULL OR distance IN ('null', '') THEN NULL
      ELSE CAST(REPLACE(REPLACE(distance, 'km', ''), ' ', '') AS FLOAT)
    END AS cleaned_distance_km,
    
    -- Clean duration: strip 'minutes', 'mins', 'minute', convert to integer
    CASE 
      WHEN duration IS NULL OR duration IN ('null', '') THEN NULL
      ELSE CAST(
        REPLACE(
          REPLACE(
            REPLACE(duration, 'minutes', ''), 
            'minute', ''
          ), 
          'mins', ''
        ) AS INT
      )
    END AS cleaned_duration_mins,
    
    -- Clean cancellation: convert 'null' and '' to NULL
    CASE 
      WHEN cancellation IN ('null', '') THEN NULL
      ELSE cancellation
    END AS cleaned_cancellation
  FROM runner_orders
)
SELECT * FROM cleaned_runner_orders;
```
