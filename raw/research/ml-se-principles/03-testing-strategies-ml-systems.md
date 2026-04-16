# Testing Strategies for ML Systems

ML systems are notoriously undertested. The non-determinism of model training, combined with the fact that there is no single "correct" output, makes engineers reluctant to write tests. But most of what makes an ML system break is deterministic and testable: the data pipeline, the feature transforms, the training loop logic, and the serving infrastructure.

## The ML Testing Pyramid

Adapt the standard testing pyramid to ML:

```
              [Model behaviour / shadow tests]
           [Integration: pipeline end-to-end smoke test]
        [Data validation: schema, distribution invariants]
     [Unit tests: feature transforms, preprocessing, loss functions]
```

The bottom two layers should have near-100% coverage and run on every commit. The upper layers run on merge to main or on a schedule.

## Unit Tests for Feature Transforms

Every feature engineering function should have unit tests with known inputs and expected outputs. This is the highest-value, lowest-effort testing you can do:

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
- Test **boundary conditions** (zero, null, empty string, extreme values).
- Test that transforms are **idempotent** when they should be (running normalisation twice should give the same result as once).
- Test **invertibility** for transforms that are supposed to be reversible.

## Data Validation Tests

Data validation should gate the pipeline before training starts. Use a schema-based approach:

- **Great Expectations** or **Pandera**: define expectations as code (checked into version control), run them as a pipeline step.
- Required checks: no unexpected nulls in required columns, value ranges within known bounds, categorical values within known set, no duplicate primary keys, row count within expected range.
- Treat validation failures as **blocking errors**, not warnings. A model trained on corrupt data produces a corrupt model.

Example Pandera schema pattern:
```python
schema = pa.DataFrameSchema({
    "age": pa.Column(int, pa.Check.in_range(0, 120)),
    "spend_usd": pa.Column(float, pa.Check.ge(0)),
    "country_code": pa.Column(str, pa.Check.isin(VALID_COUNTRY_CODES)),
})
schema.validate(df)  # raises SchemaError if violated
```

## Model Validation Tests

After training, before registration, run automated validation:

- **Performance regression**: model must exceed a minimum threshold on a fixed held-out test set. This threshold should be version-controlled, not hardcoded by an individual.
- **Slice evaluation**: check that performance is acceptable across defined subgroups (age bands, device type, geography). Aggregate metrics can mask catastrophic failure on a minority slice.
- **Prediction sanity checks**: assert that predictions are in the expected range (probabilities sum to 1, regression output is not negative for quantities that must be positive).
- **Inference latency**: run a benchmark batch through the model; assert p99 latency is within SLA before the model can be promoted.

## Integration Tests for Pipelines

Run a lightweight end-to-end smoke test on every merge using a small synthetic dataset:

- Covers: data loading → preprocessing → training (few steps) → evaluation → artefact logging → model loading for inference.
- Does **not** need to produce a good model — it just needs to complete without error.
- Catches integration breaks (schema mismatches between pipeline steps, missing environment variables, broken artefact paths) before they reach production.

## Testing the Training Loop

For custom training code, test the mechanics:

- **Overfit test**: given a tiny batch (1–10 examples), the model should be able to drive loss to near zero in a few hundred steps. If it can't, the loss function or optimiser is broken.
- **Gradient flow test**: assert that all parameters have non-zero gradients after a backward pass.
- **Determinism test**: given the same seed and batch, two forward passes should produce identical outputs.

## Source

- Breck, E., Cai, S., Nielsen, E., Salib, M., & Sculley, D. (2017). **The ML Test Score: A Rubric for ML Production Readiness and Technical Debt Reduction**. *IEEE International Conference on Big Data*. https://research.google/pubs/pub46555/
- Huyen, C. (2022). *Designing Machine Learning Systems*, Chapter 8: Data Distribution Shifts and Monitoring. O'Reilly.
- Klaise, J., Van Looveren, A., Cox, C., Vacanti, G., & Coca, A. (2020). Monitoring and explainability of models in production. *ICML Workshop*. https://arxiv.org/abs/2007.06299
- Great Expectations documentation: https://docs.greatexpectations.io
- Pandera documentation: https://pandera.readthedocs.io
