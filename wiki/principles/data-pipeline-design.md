# Data Pipeline Design

Data pipelines are software. They should be tested, versioned, and designed with the same principles as application code — single responsibility, clear contracts, and idempotency.

## Core Principles

### Idempotency
A pipeline stage is idempotent if running it twice produces the same result as running it once. Design every transform so it can be safely re-run: either check if output already exists, or overwrite deterministically rather than appending. Idempotent pipelines are dramatically easier to debug, recover, and test.

### Single Responsibility per Stage
Each function or stage should do exactly one thing: load, validate, clean, featurise, train, or evaluate. A function that loads data, applies three transforms, and writes output is three functions badly compressed into one. This principle connects directly to [[principles/single-responsibility]].

### Validate Before You Transform
Validate inputs at the pipeline boundary before any transformation. Catching a schema violation at ingestion is trivial; catching it at model training is expensive and produces misleading error messages.

Use **Pandera** or **Great Expectations** to encode schemas as version-controlled code:

```python
import pandera as pa

schema = pa.DataFrameSchema({
    "customer_id": pa.Column(str, nullable=False),
    "revenue_usd": pa.Column(float, pa.Check.ge(0)),
    "signup_date": pa.Column(pa.DateTime),
    "country_code": pa.Column(str, pa.Check.isin(VALID_COUNTRIES)),
})

validated_df = schema.validate(raw_df)  # raises SchemaError on violation
```

Treat validation failures as **blocking errors**, not warnings. Never train on corrupt data.

### Raw Data is Immutable
The raw data layer is read-only. All transforms write to a separate output path. This makes pipelines re-runnable from the original source without ambiguity about what was modified. See [[workflow/project-structure]].

## Pandas Best Practices

### Prefer Method Chaining
Chains produce readable, linear transformations without intermediate variable proliferation:

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

### Use `.loc` / `.iloc` for Assignment
Chained indexing (`df["a"]["b"] = value`) causes a `SettingWithCopyWarning` and may silently fail:

```python
# Wrong
df["flag"][df["score"] > 0.9] = 1

# Correct
df.loc[df["score"] > 0.9, "flag"] = 1
```

### Categoricals for Memory Efficiency
Low-cardinality string columns should be `pd.Categorical` — reduces memory by 50–90% and speeds up `groupby`:

```python
df["region"] = df["region"].astype("category")
```

### Vectorise Before Applying
`iterrows` is ~100x slower than vectorised operations. Before reaching for `apply()`, check whether the operation can be expressed with built-in vectorised methods, `np.where`, or `pd.cut`.

## NumPy Best Practices

- Use broadcasting instead of Python loops over array elements
- Specify `dtype` explicitly for large arrays (`dtype=np.float32` for neural net inputs saves memory)
- Use `np.random.default_rng(seed)` instead of `np.random.seed()` — the legacy API is problematic in multi-threaded code

## Training-Serving Skew

The most common and costly ML production bug. It occurs when features are computed differently in training and serving. Prevention:
- Encapsulate every feature transform in a pure function in `src/`
- Use the **same code path** for feature computation at training and serving time
- Use a **feature store** to materialise features once and retrieve them consistently at both stages

See [[principles/ml-technical-debt]] and [[principles/reproducibility]].

## Source
- Pandas documentation: https://pandas.pydata.org/docs/
- Pandera: https://pandera.readthedocs.io/
- Kedro: https://kedro.readthedocs.io/

## Related
- [[principles/single-responsibility]]
- [[principles/io-contracts]]
- [[principles/ml-technical-debt]]
- [[testing/ml-testing-strategies]]
- [[workflow/project-structure]]
