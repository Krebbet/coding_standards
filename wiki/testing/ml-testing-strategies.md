# ML Testing Strategies

Most of what makes an ML system break is deterministic and fully testable: the data pipeline, the feature transforms, the training loop logic, and the serving infrastructure. Non-determinism in model training is not an excuse to avoid testing.

## The ML Testing Pyramid

```
              [Model behaviour / shadow tests]       ← run on schedule or pre-promotion
           [Integration: pipeline end-to-end smoke]  ← run on merge to main
        [Data validation: schema + distribution]      ← run pre-training
     [Unit tests: feature transforms, loss functions] ← run on every commit
```

The bottom two layers should have near-100% coverage and run on every commit. The upper layers run on merge or on a schedule.

## Unit Tests for Feature Transforms

Every feature engineering function must have unit tests with known inputs and expected outputs:

```python
def test_log1p_transform_handles_zero():
    result = log1p_transform(pd.Series([0, 1, 9]))
    assert result.iloc[0] == 0.0
    assert abs(result.iloc[1] - 0.693) < 1e-3

def test_bucketise_age_returns_correct_bin():
    assert bucketise_age(0) == "0-17"
    assert bucketise_age(18) == "18-34"
    assert bucketise_age(99) == "65+"
```

Key patterns:
- Test **boundary conditions** (zero, null, empty, extreme values)
- Test that transforms are **idempotent** where they should be
- Test **invertibility** for reversible transforms
- Test with explicitly **typed inputs** — schema mismatches are a primary failure mode

## Data Validation Tests

Gate the pipeline before training starts. Use schema-based approaches:

```python
import pandera as pa

schema = pa.DataFrameSchema({
    "age": pa.Column(int, pa.Check.in_range(0, 120)),
    "spend_usd": pa.Column(float, pa.Check.ge(0)),
    "country_code": pa.Column(str, pa.Check.isin(VALID_COUNTRY_CODES)),
})

schema.validate(df)  # raises SchemaError if violated
```

Required checks:
- No unexpected nulls in required columns
- Value ranges within known bounds
- Categorical values within the known set
- No duplicate primary keys
- Row count within expected range (a 90% row-count drop is data corruption, not a schema violation)

Treat validation failures as **blocking errors** — never train on corrupt data.

## Model Validation Tests

After training, before model registry promotion, run automated validation:

- **Performance regression** — model must exceed a minimum threshold on a fixed held-out test set. The threshold is version-controlled, not set by an individual
- **Slice evaluation** — check performance across defined subgroups (age bands, device type, geography). Aggregate metrics can mask catastrophic failure on a minority slice
- **Prediction sanity checks** — probabilities sum to 1, regression outputs are not negative for quantities that must be positive, confidence scores are in [0, 1]
- **Inference latency** — assert p99 latency is within SLA before a model can be promoted

## Integration Smoke Test

Run a lightweight end-to-end smoke test on every merge using a small synthetic dataset:

- Covers: data loading → preprocessing → training (few steps) → evaluation → artefact logging → model loading for inference
- Does **not** need to produce a good model — it just needs to complete without error
- Catches integration breaks: schema mismatches between pipeline steps, missing environment variables, broken artefact paths

## Training Loop Tests

For custom training code:

- **Overfit test** — given a tiny batch (1–10 examples), the model should drive loss to near zero in a few hundred steps. If it can't, the loss function or optimiser is broken
- **Gradient flow test** — assert that all trainable parameters have non-zero gradients after a backward pass
- **Determinism test** — given the same seed and batch, two forward passes must produce identical outputs

## Source
- Breck et al. (2017). The ML Test Score. IEEE Big Data. https://research.google/pubs/pub46555/
- Huyen, C. (2022). *Designing ML Systems*, Chapter 8. O'Reilly.
- Great Expectations: https://docs.greatexpectations.io
- Pandera: https://pandera.readthedocs.io

## Related
- [[testing/testing-philosophy]]
- [[testing/writing-good-tests]]
- [[principles/data-pipeline-design]]
- [[principles/ml-technical-debt]]
- [[cloud/cicd-for-ml]]
