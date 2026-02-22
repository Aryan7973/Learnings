# COALESCE() in SQL

## 📌 Definition

`COALESCE()` is a SQL function that returns the **first non-NULL value** from a list of expressions.

### Syntax

```sql
COALESCE(value1, value2, value3, ...)
```

- Evaluates expressions **from left to right**
- Returns the **first value that is NOT NULL**

---

# Why COALESCE() is Important

In databases:

- NULL values are very common
- NULL breaks calculations
- NULL causes display issues
- NULL complicates logic

`COALESCE()` helps to:

- Replace NULL values
- Provide fallback values
- Avoid NULL in calculations
- Clean output for reports

---

# Basic Example

```sql
SELECT COALESCE(NULL, NULL, 5, 10);
```

**Result:**
```
5
```

Because `5` is the first non-NULL value.

---

# Real-World Example: Employee Bonus

## Without COALESCE

```sql
SELECT name, bonus 
FROM employees;
```

If an employee has no bonus → result shows `NULL`.

---

## Using COALESCE to Replace NULL

```sql
SELECT name, COALESCE(bonus, 0) AS bonus
FROM employees;
```

Now employees with no bonus will show:

```
0
```

---

# Using COALESCE in Calculations

## Problem

```sql
SELECT salary + bonus
FROM employees;
```

If `bonus` is NULL → result becomes NULL ❌

---

## Solution

```sql
SELECT salary + COALESCE(bonus, 0)
FROM employees;
```

Now NULL bonus is treated as 0 ✅

---

# COALESCE vs IFNULL (MySQL)

MySQL also provides:

```sql
IFNULL(expression, replacement)
```

Example:

```sql
IFNULL(bonus, 0)
```

---

## Comparison

| COALESCE | IFNULL |
|-----------|--------|
| Can take multiple values | Only 2 arguments |
| Standard SQL | MySQL specific |
| More flexible | Simpler |

---

# Using COALESCE in ORDER BY

```sql
SELECT *
FROM employees
ORDER BY COALESCE(bonus, 0) DESC;
```

Sorts employees by bonus, treating NULL as 0.

---

# Using COALESCE in WHERE

```sql
SELECT *
FROM employees
WHERE COALESCE(bonus, 0) > 1000;
```

Filters employees with bonus greater than 1000 (NULL treated as 0).

---

# Data Type Behavior

MySQL determines the return type based on the **first non-null expression**.

Example:

```sql
SELECT COALESCE(NULL, 'hello', 5);
```

⚠ Be careful when mixing data types.

---

# Performance Note ⚠

`COALESCE()` is lightweight and efficient.

However, avoid using it on indexed columns inside `WHERE` unnecessarily:

```sql
WHERE COALESCE(bonus, 0) = 500
```

This can prevent index usage.

---

## Better Approach

```sql
WHERE bonus = 500 OR bonus IS NULL;
```

This allows better index optimization.

---

# ✅ Summary

- Returns first non-NULL value
- Evaluates left → right
- Helps clean NULL issues
- Useful in SELECT, WHERE, ORDER BY
- Standard SQL function
- Avoid wrapping indexed columns unnecessarily
