# Pandas: Vector, Lambda.apply(), Iterrows Efficiency

Last Edit: April 5, 2023 6:42 PM
Level: Variable

## Pandas: Vector, Lambda apply, & Iterrows

Avoid using lambdas where simpler operations work, especially when using .apply() on large dataframes. For performance reasons, dataframe.iterrows() should be avoided at all costs. 

Use it only if vectorized methods, apply() and [itertuples()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.itertuples.html) cannot solve the issue (they almost certainly can).

tl;dr fastest operations in order:

1. Direct column vector manipulations - fastest!
2. Apply with Lambda - decent
3. Itertuples - marginal
4. Iterrows - avoid if at all possible

**Bad:**

```python
# Simple operation with lambda
df["time_to_convert"] = df.apply(lambda row: row["first_sale"] - row["first_call"], axis=1)

# Operation on the whole dataframe with lambda
df["days_to_convert"] = df.apply(lambda row: row["time_to_convert"].days, axis=1)

# iterrows() to create a new column based on row values
animals = pd.DataFrame({
    'name': ['dormousemouse', 'blue whale', 'sloth', 'kakapo'],
	'weight': [0.052, 441000, 12, 7]})

for index, row in animals.iterrows():
    if row['weight'] > 100:
        animals.loc[index, 'size'] = 'large'
    else:
        animals.loc[index, 'size'] = 'relatively small'
```

**Good:**

```python

# For simple operations just do the vector operation is MUCH faster
df["time_to_convert"] = df["first_sale"] - df["first_call"]

# access series directly instead of entire dataframes whenever possible
df["days_to_convert"] = df["first_range"].apply(lambda row: row.days)

# apply() on a series with lambda instead of itterows is much faster
df_animals = pd.DataFrame(
	{'name': ['dormouse', 'blue whale', 'sloth', 'kakapo'],
	'weight': [0.052, 441000, 12, 7]})
df_animals['size'] = df_animals['weight'].apply(lambda row: 'large' if row > 100 else 'relatively small')
```