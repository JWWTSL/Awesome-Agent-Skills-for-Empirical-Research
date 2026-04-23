---
name: statspai
description: Agent-native causal inference and econometrics toolkit for Python. 390+ functions, one import, unified API. Covers OLS, IV, DID, staggered DID, RDD, PSM, SCM, DML, Causal Forest, Meta-Learners, TMLE, neural causal models, and more. Use when the user asks about treatment effect estimation, causal analysis, regression analysis, policy evaluation, observational study methods, or any of the listed econometric methods in Python. Every function returns structured result objects with self-describing schemas for LLM-driven workflows.
triggers:
  - implement causal inference
  - run a DID analysis
  - instrumental variables regression
  - regression discontinuity
  - propensity score matching
  - synthetic control
  - double machine learning
  - causal forest
  - panel data regression
  - econometric analysis
  - treatment effect estimation
  - causal analysis
  - policy evaluation
  - observational study
  - StatsPAI
  - statspai
---

# StatsPAI: Agent-Native Causal Inference & Econometrics

StatsPAI is the agent-native Python package for causal inference and applied econometrics. One `import statspai as sp`, 390+ functions, covering the complete empirical research workflow.

**Source**: https://github.com/brycewang-stanford/StatsPAI
**PyPI**: `pip install statspai`
**Paper**: Published in Journal of Open Source Software (JOSS)

## Why StatsPAI for Agents?

StatsPAI is the **first econometrics toolkit purpose-built for LLM-driven research workflows**:

1. **Self-describing API**: `sp.list_functions()`, `sp.describe_function("did")`, `sp.function_schema("rdrobust")` — agents can discover and understand functions without documentation lookup
2. **Unified result objects**: Every function returns a `CausalResult` with `.summary()`, `.plot()`, `.to_latex()`, `.to_word()`, `.to_excel()`, `.cite()`
3. **One import**: No need to juggle 20+ packages — `import statspai as sp` covers everything
4. **Publication-ready output**: Word, Excel, LaTeX, HTML export in every function

## Core Methods

### Classical Econometrics
```python
sp.regress(df, "y ~ x1 + x2", cluster="firm_id")        # OLS
sp.ivreg(df, "y ~ x1 | z1 + z2", cluster="state")        # IV/2SLS
sp.panel(df, "y ~ x1 + x2", entity="firm", time="year", model="fe")  # Panel FE
sp.heckman(df, "y ~ x1", "select ~ z1 + z2")              # Heckman selection
sp.qreg(df, "y ~ x1 + x2", quantile=0.5)                  # Quantile regression
```

### Difference-in-Differences
```python
sp.did(df, "y", "treated", "post")                         # Auto-dispatch (2x2 or staggered)
sp.callaway_santanna(df, "y", "group", "time")             # Staggered DID (CS 2021)
sp.sun_abraham(df, "y", "cohort", "time")                  # Interaction-weighted event study
sp.bacon_decomposition(df, "y", "treated", "time")         # TWFE diagnostic
sp.honest_did(result, method="smoothness")                 # Sensitivity to PT violations
sp.continuous_did(df, "y", "dose", "time")                 # Continuous treatment
```

### Regression Discontinuity
```python
sp.rdrobust(df, "y", "running_var", cutoff=0)              # Sharp RD (CCT 2014)
sp.rdrobust(df, "y", "running_var", fuzzy="treatment")     # Fuzzy RD
sp.rddensity(df, "running_var")                            # McCrary density test
sp.rdmc(df, "y", "running_var", cutoffs=[0, 5, 10])        # Multi-cutoff RD
sp.rkd(df, "y", "running_var", cutoff=0)                   # Regression kink design
```

### Matching & Reweighting
```python
sp.match(df, "treatment", covariates, method="psm")        # Propensity score matching
sp.match(df, "treatment", covariates, method="cem")        # Coarsened exact matching
sp.ebalance(df, "treatment", covariates)                   # Entropy balancing
```

### Synthetic Control
```python
sp.synth(df, "y", "unit", "time", treated_unit=1, treated_period=2000)  # ADH SCM
sp.sdid(df, "y", "unit", "time", treated_units, treated_periods)        # Synthetic DID
```

### Machine Learning Causal Inference
```python
sp.dml(df, "y", "treatment", controls, model="PLR")       # Double/Debiased ML
sp.causal_forest(df, "y", "treatment", controls)           # Causal Forest (GRF)
sp.metalearner(df, "y", "treatment", controls, learner="dr")  # DR-Learner
sp.tmle(df, "y", "treatment", controls)                    # Targeted MLE
sp.aipw(df, "y", "treatment", controls)                    # Augmented IPW
```

### Neural Causal Models
```python
sp.tarnet(df, "y", "treatment", controls)                  # TARNet
sp.cfrnet(df, "y", "treatment", controls)                  # CFRNet
sp.dragonnet(df, "y", "treatment", controls)               # DragonNet
```

### Robustness & Workflow
```python
sp.spec_curve(df, "y", "treatment", controls, specs)       # Specification curve
sp.robustness_report(result)                               # Automated robustness report
sp.subgroup_analysis(df, "y", "treatment", subgroups)      # Heterogeneity with Wald test
result.to_latex()                                          # Export to LaTeX
result.to_word("output.docx")                              # Export to Word
result.cite()                                              # Auto-generate citation
```

### Interactive Visualization (v0.6+)
```python
fig = result.plot()
sp.interactive(fig)  # Stata Graph Editor-style WYSIWYG editing, 29 academic themes
```

## Agent Integration Pattern

```python
import statspai as sp

# Step 1: Discover available functions
functions = sp.list_functions()

# Step 2: Understand a specific function
info = sp.describe_function("callaway_santanna")

# Step 3: Get JSON schema for structured calls
schema = sp.function_schema("callaway_santanna")

# Step 4: Execute and get structured results
result = sp.callaway_santanna(df, "y", "group", "time")
print(result.summary())
result.to_latex("tables/did_results.tex")
```

## When to Use StatsPAI vs Other Packages

| Scenario | Use StatsPAI | Alternative |
|----------|-------------|-------------|
| Agent-driven analysis pipeline | ✅ Best choice — self-describing API | pyfixest (no agent API) |
| Full causal inference workflow | ✅ 390+ functions, one import | Assemble 10+ R/Python packages |
| Publication-ready output needed | ✅ Word/Excel/LaTeX/HTML built-in | statsmodels (no export) |
| Staggered DID with diagnostics | ✅ CS + SA + Bacon + HonestDID | differences (partial) |
| Neural causal models | ✅ TARNet/CFRNet/DragonNet | econml (partial) |
| Stata users migrating to Python | ✅ Stata-equivalent function names | linearmodels (limited) |

## Validation and Error Handling

After running any estimation, check the result object before proceeding:

```python
result = sp.did(df, "y", "treated", "post")

# Always inspect the human-readable summary first
print(result.summary())

# Inspect structured diagnostics for agent-driven logic
if hasattr(result, "diagnostics"):
    print(result.diagnostics)
```

Common pitfalls to guard against:

- **Convergence warnings** — surfaced in `result.summary()` (check before trusting SEs).
- **Weak instruments** for IV — require first-stage F ≥ 10 (Stock–Yogo rule of thumb); StatsPAI exposes this in `result.diagnostics["First-stage F (<endog>)"]`.
- **Parallel trends** for DID — run an event study and verify pre-treatment coefficients are statistically indistinguishable from zero; follow up with `sp.honest_did(result)` for Rambachan–Roth sensitivity.
- **Bandwidth sensitivity** for RDD — re-run `sp.rdrobust` at half and double the MSE-optimal bandwidth; agreement within one SE is reassuring.
- **Missing data** — StatsPAI drops rows with missing values by default; check `result.data_info["n_obs"]` matches your expected sample size.
- **Overlap / common support** — for matching, DML, and meta-learners, inspect propensity-score distributions before interpreting CATEs.
