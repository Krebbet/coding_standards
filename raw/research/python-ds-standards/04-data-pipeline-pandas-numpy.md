# Data Pipeline Design and Pandas/NumPy Best Practices

## Source

- https://pandas.pydata.org/docs/user_guide/indexing.html
- https://numpy.org/doc/stable/user/basics.html
- https://pandas.pydata.org/docs/user_guide/enhancingperf.html
- https://tomaugspurger.net/posts/modern-pandas-part-1/
- https://kedro.readthedocs.io/en/stable/

---

## Data Pipeline Design Principles

### Make Pipelines Idempotent

A pipeline step is idempotent if running it twice produces the same result as running it once. Write every transform so it can be safely re-run: check whether output already exists, or overwrite deterministically rather than appending. This makes debugging far easier.

### Separate Data Validation from Transformation

Validate inputs at the boundary before transforming them. Libraries like **Pandera** or **Great Expectations** let you encode schema expectations as code:

```python
import pandera as pa

schema = pa.DataFrameSchema({
    "customer_id": pa.Column(str, nullable=False),
    "revenue_usd": pa.Column(float, pa.Check.ge(0)),
    "signup_date": pa.Column(pa.DateTime),
})

validated_df = schema.validate(raw_df)  # raises SchemaError on violation
```

Catching schema violations at ingestion prevents silent corruption that surfaces as misleading model metrics downstream.

### Pipeline Stages Should Have Single Responsibilities

Each function or pipeline stage should do one thing: load, clean, featurise, train, or evaluate. Functions that do all of these are hard to test and reuse. Kedro formalises this pattern with its node/pipeline abstraction, but the principle applies even in simple Makefiles.

---

## Pandas Best Practices

### Prefer Method Chaining

Method chaining produces readable, linear transformations and avoids intermediate variable proliferation:

```python
clean = (
    raw_df
    .rename(columns=str.lower)
    .dropna(subset=["customer_id", "revenue_usd"])
    .assign(revenue_eur=lambda df: df["revenue_usd"] * 0.92)
    .query("revenue_usd > 0")
    .reset_index(drop=True)
)
```

### Never Use Chained Indexing for Assignment

`df["col1"]["col2"] = value` triggers a `SettingWithCopyWarning` and may silently fail. Always use `.loc` or `.iloc` for assignment:

```python
# Wrong
df["flag"][df["score"] > 0.9] = 1

# Correct
df.loc[df["score"] > 0.9, "flag"] = 1
```

### Use Categoricals for Low-Cardinality String Columns

Converting string columns with few unique values to `pd.Categorical` can reduce memory usage by 50–90% and speeds up `groupby` operations:

```python
df["region"] = df["region"].astype("category")
```

### Avoid `iterrows` and `apply` for Vectorisable Operations

`iterrows` is roughly 100x slower than vectorised operations. Before using `apply`, ask whether the operation can be expressed with built-in vectorised methods, `np.where`, or `pd.cut`.

---

## NumPy Best Practices

### Use Broadcasting Instead of Loops

NumPy's broadcasting rules allow operations on arrays of compatible shapes without explicit iteration. When you find yourself writing a Python loop over array elements, there is almost always a vectorised alternative.

### Specify `dtype` Explicitly for Large Arrays

Creating arrays without a dtype lets NumPy infer it, which defaults to `float64`. For large arrays where `float32` suffices (e.g., neural network inputs), specify it:

```python
X = np.zeros((n_samples, n_features), dtype=np.float32)
```

### Avoid Copies When Not Needed

Many NumPy operations return views, not copies. Be explicit: use `.copy()` only when you need an independent array, and avoid unnecessary copies in tight loops.

### Fix Random State with `np.random.default_rng`

The legacy `np.random.seed()` global state is problematic in multi-threaded code and test suites. Use the modern generator API:

```python
rng = np.random.default_rng(seed=42)
samples = rng.standard_normal(size=(1000, 10))
```

Pass `rng` as a parameter to functions that need randomness to make them testable and reproducible without relying on global state.
