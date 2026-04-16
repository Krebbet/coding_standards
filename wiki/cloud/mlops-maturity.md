# MLOps Maturity and Principles

MLOps applies DevOps principles — automation, continuous delivery, monitoring — to the ML model lifecycle. It treats model training and deployment as a software engineering problem, not a one-off research task.

## The Three Levels of MLOps Maturity (Google)

### Level 0 — Manual Process
Data scientists train models manually and deliver a frozen model artifact to engineering for deployment. No pipeline automation, no CI/CD for models. Retraining is manual and infrequent.

**Appropriate for:** Very early-stage or low-stakes systems. **Not** appropriate as a permanent state.

### Level 1 — ML Pipeline Automation
The training pipeline is automated and can be triggered on a schedule or by data drift. Feature engineering is part of the pipeline, not a separate notebook. Models are retrained continuously, but changes to pipeline code are still manual.

**Target for most teams before investing in Level 2 infrastructure.**

### Level 2 — CI/CD Pipeline Automation
Changes to pipeline code trigger automated build, test, and deployment of new pipeline versions. A change to a feature transform goes through the same CI/CD process as a change to a web service.

**Target state for any system where model quality directly affects business outcomes.**

## Continuous Training

Unlike traditional software where a deployed version is stable until changed, ML models decay as the world changes. Retraining triggers:

- **Time-based** — retrain weekly/monthly on a fixed schedule
- **Volume-based** — retrain every N new labelled examples
- **Drift-based** — retrain when KL divergence between live and training distributions exceeds a threshold

The full pipeline must be automated so retraining requires no human intervention for the happy path. Gate automated promotion on automated evaluation — the new model must beat the current production model before it can deploy.

## Feature Engineering as Software

Feature engineering is the highest-leverage part of most ML pipelines and frequently the most poorly engineered.

**Encapsulate transforms in pure functions** — input data → output data, no side effects, no global state. This makes transforms testable, reusable, and debuggable. See [[principles/data-pipeline-design]].

**Separate computation from retrieval** — compute features once, store them, and retrieve from the same store at both training and serving time. Computing features differently in training vs serving is the root cause of training-serving skew.

**Version feature sets explicitly** — a "feature set" is a named, versioned collection of features. When the set changes, the version increments. Each model is tied to a specific feature set version, not to "the latest features."

**Document every feature** — a column named `f_42` is a liability. Every feature needs:
- A name communicating what it measures
- A description of how it is computed
- The unit or range of values
- Known issues or caveats

## Model Health Monitoring

A deployed model has two kinds of health:

- **System health** — latency, error rate, throughput. See [[cloud/observability]].
- **Model health** — prediction distribution drift, feature distribution drift, label drift, business metric degradation.

Minimum effective monitoring:
1. Log every prediction with its input features (sampled at high volume)
2. Compute daily summary statistics on feature distributions vs training baseline
3. Alert when any feature's PSI > 0.2 or prediction mean shifts > 2 standard deviations
4. Where ground truth is available with a lag, compute rolling model performance and alert on degradation

## Source
- Google Cloud — MLOps whitepaper: https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning
- Kreuzberger et al. (2022). MLOps: Overview, Definition, and Architecture. https://arxiv.org/abs/2205.02302
- Huyen, C. (2022). *Designing ML Systems*, Chapter 10. O'Reilly.

## Related
- [[cloud/cicd-for-ml]]
- [[cloud/observability]]
- [[principles/reproducibility]]
- [[principles/ml-technical-debt]]
- [[principles/data-pipeline-design]]
