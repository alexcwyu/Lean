# LEAN optional Python environments

These four virtual uv projects execute existing macOS ARM package workflows in genuine child interpreters. They preserve the main Python3.11 scientific core. Each project has a minor interpreter pin, version constraints and its own uv.lock; .venv is ignored. No source or fake module from another environment is injected into a child.

| Project | Managed interpreter | Existing cases and compatibility reason |
|---|---|---|
| [pandas2-tfdf](pandas2-tfdf/pyproject.toml) |3.12 | Alphalens, Copulae, Copulas, GluonTS, Pyfolio, Modin and TF Decision Forests require pandas2 or TensorFlow2.19. YDF0.15 is the latest inspected compatible protobuf5-generated wheel. |
| [numpy1-gpflow](numpy1-gpflow/pyproject.toml) |3.12 | FuzzyCMeans requires NumPy1; Lingam requires SciPy<=1.13.1. GPflow on macOS ARM uses TensorFlow-macos2.16.2, compatible TFP0.24 and tf-keras2.16. TensorTrade1.0.4 requires Python>=3.12; Stochastic0.6 is the newest dependency release admitting3.12. |
| [pandas-ta](pandas-ta/pyproject.toml) |3.13 | Current PandasTA0.4.71b0 requires Python>=3.12, while its fixed Numba0.61.2 provides interpreter wheels only through3.13. Its public version attribute is `version`. |
| [pystan](pystan/pyproject.toml) |3.14 | Latest PyStan3.14/httpstan4.17 supplies universal stanc2.39.0. Httpstan has no cp315 wheel. The existing8-schools model and4chains/1000samples workflow stays unchanged. |

From the LEAN root, install each genuine environment before running the existing NUnit package fixture:

```sh
uv sync --locked --project python-environments/pandas2-tfdf
uv sync --locked --project python-environments/numpy1-gpflow
uv sync --locked --project python-environments/pandas-ta
uv sync --locked --project python-environments/pystan
```

The [existing fixture helper](../Tests/Python/PythonPackagesTests.cs) locates these projects relative to the test assembly, uses their actual `.venv/bin/python`, removes inherited `PYTHONPATH`, `PYTHONHOME`, `PYTHONNET_PYDLL` and `VIRTUAL_ENV`, and prepends the child executable directory to PATH. Each original RunTest executes with captured stdout/stderr, an asserted real exit status and a bounded owned process-tree timeout. Other platforms keep their existing embedded behavior. Absent environments fail the original case with the install command. All original168 test attributes and30 existing explicit flags are retained; no additional cases are defined here.

Full engine prerequisites and exact test evidence are in [development](../docs/development.md) and [validation](../docs/validation.md). The Docker Python image investigated for the earlier Intel-only Stan compiler is unnecessary with the current native cohort.
