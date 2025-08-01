
## 🔍 What is `.transform()` in Pandas?

```python
DataFrame/Series.transform(func)
```

The `.transform()` function **applies a function to each group or column/row individually**, **but returns an object of the same size** as the input. It’s often used with **groupby()** to broadcast aggregated values **back to the original shape**.

---

## ✅ Purpose of `.transform()`

* To **apply a function** (like mean, standard deviation, etc.)
* And **return a series/dataframe of the same shape**
* Useful when you want to **add back calculated values to the original data** (like normalized scores, group-wise means, etc.)

---

## 📘 Basic Example: Column-wise transformation

```python
import pandas as pd

df = pd.DataFrame({
    'A': [1, 2, 3, 4],
    'B': [10, 20, 30, 40]
})

# Multiply every value in column A by 2
df['A_transformed'] = df['A'].transform(lambda x: x * 2)
print(df)
```

### Output:

```
   A   B  A_transformed
0  1  10             2
1  2  20             4
2  3  30             6
3  4  40             8
```

✅ **Same shape is preserved.**
Unlike `.apply()`, which can reduce dimensions, `.transform()` **retains the original row count**.

---

## 📊 Example with `groupby()` — Group-wise Mean

```python
df = pd.DataFrame({
    'Dept': ['HR', 'HR', 'IT', 'IT', 'Finance'],
    'Salary': [30000, 32000, 45000, 47000, 40000]
})

# Calculate group-wise mean and assign it to each row
df['Dept_Avg_Salary'] = df.groupby('Dept')['Salary'].transform('mean')
print(df)
```

### Output:

```
     Dept  Salary  Dept_Avg_Salary
0      HR   30000          31000.0
1      HR   32000          31000.0
2      IT   45000          46000.0
3      IT   47000          46000.0
4  Finance   40000          40000.0
```

✅ **Each employee gets their department's average salary** — repeated for each row.

---

## 📐 Difference between `.apply()` and `.transform()`

| Feature            | `.apply()`                    | `.transform()`                   |
| ------------------ | ----------------------------- | -------------------------------- |
| Output shape       | May reduce or change shape    | Always matches input shape       |
| Used for           | Aggregation or transformation | Broadcasting values              |
| Works with groupby | Yes                           | Yes                              |
| Ideal for          | Group-level summary values    | Row-wise values from group logic |

---

## 🔁 Built-in Functions with `transform`

You can pass:

* A **string name**: `'mean'`, `'sum'`, `'max'`
* A **custom lambda**: `lambda x: x - x.mean()`
* A **function object**: `np.sqrt`

```python
df['Z_Score'] = df.groupby('Dept')['Salary'].transform(lambda x: (x - x.mean()) / x.std())
```

---

## 🧠 When to Use `.transform()`?

| Use Case                           | `.transform()` is Useful?    |
| ---------------------------------- | ---------------------------- |
| Normalizing values within groups   | ✅ Yes                        |
| Broadcasting group metrics to rows | ✅ Yes                        |
| Reducing a group to a single value | ❌ Use `.agg()` or `.apply()` |
| Changing number of rows or columns | ❌ Use `.apply()`             |

---

## 🧪 Real-world Example: Adding group-wise % share

```python
df['Group_Total'] = df.groupby('Dept')['Salary'].transform('sum')
df['Percent_Share'] = df['Salary'] / df['Group_Total']
```

Now every row contains:

* Their department's total salary
* Their percentage share within the group

---

## ✅ Summary

| Feature         | Explanation                                              |
| --------------- | -------------------------------------------------------- |
| Returns         | Same shape as input (Series or DataFrame)                |
| Common use with | `.groupby()` for group-wise row-level calculations       |
| Ideal for       | Normalizing, filling, reweighting, per-row group metrics |
| Works on        | Series or DataFrame                                      |

---

Here is a **side-by-side comparison of `.apply()`, `.agg()`, and `.transform()`** using a simple dataset — ideal for understanding how they differ in behavior, output shape, and use cases.

---

### 🧪 Example DataFrame

```python
import pandas as pd

df = pd.DataFrame({
    'Dept': ['HR', 'HR', 'IT', 'IT', 'Finance'],
    'Salary': [30000, 32000, 45000, 47000, 40000]
})
```

---

## 📊 Comparison Table

| Feature               | `groupby().apply()`                     | `groupby().agg()`                   | `groupby().transform()`                        |
| --------------------- | --------------------------------------- | ----------------------------------- | ---------------------------------------------- |
| ✅ **Purpose**         | General function across groups          | Aggregation (summarize each group)  | Broadcast result of group function to each row |
| 🔁 **Output Shape**   | Can be different (summary, lists, etc.) | One row per group                   | Same shape as input                            |
| 📦 **Returns**        | Often Series or DataFrame (flexible)    | DataFrame or Series with fewer rows | Series or DataFrame with same length           |
| 🧮 **Common Use**     | Custom group logic, combined info       | Sum, Mean, Count, etc.              | Group-wise normalization, z-scores, filling    |
| 📌 **Modifies Rows?** | No (usually summarized)                 | No                                  | Yes (row-wise return)                          |

---

## 🔍 Visual Code Comparison

### 1️⃣ `groupby().apply()`

```python
df.groupby('Dept')['Salary'].apply(lambda x: x.max())
```

📤 **Output**:

```
Dept
Finance    40000
HR         32000
IT         47000
Name: Salary, dtype: int64
```

✅ Group-wise maximum — **summary only**

---

### 2️⃣ `groupby().agg()`

```python
df.groupby('Dept')['Salary'].agg(['mean', 'max'])
```

📤 **Output**:

```
            mean    max
Dept                    
Finance  40000.0  40000
HR       31000.0  32000
IT       46000.0  47000
```

✅ Multi-metric aggregation — **summarized per group**

---

### 3️⃣ `groupby().transform()`

```python
df['Dept_Mean'] = df.groupby('Dept')['Salary'].transform('mean')
```

📤 **Output**:

```
     Dept  Salary  Dept_Mean
0      HR   30000   31000.0
1      HR   32000   31000.0
2      IT   45000   46000.0
3      IT   47000   46000.0
4  Finance   40000   40000.0
```

✅ **Each row gets the group mean salary** — same number of rows

---

## 📘 Summary Table with Ideal Use Cases

| Scenario                                         | Use `apply()`? | Use `agg()`? | Use `transform()`?  |
| ------------------------------------------------ | -------------- | ------------ | ------------------- |
| Get max salary per department                    | ✅              | ✅            | ❌ (no broadcasting) |
| Get mean & max salary per department             | ❌              | ✅            | ❌                   |
| Add department's mean salary to each row         | ❌              | ❌            | ✅                   |
| Add z-score within each department (row-wise)    | ❌              | ❌            | ✅                   |
| Create new structure like list of salaries/group | ✅              | ❌            | ❌                   |

---



