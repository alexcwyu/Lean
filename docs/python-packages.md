# LEAN existing Python package workflows

All **138 existing non-explicit package cases pass** in the final combined run: **170 passed, 0 failed, 0 skipped**, 157.06 seconds, exit 0. The other 32 cases are 30 original data-feed cases, the paper-order case and real virtual-environment activation. This verifies genuine numerical, solver and training workflows plus exact version assertions. The complete 40,442-case engine suite is a separate parent-run check; its earlier 40,322 passed / 20 failed / 100 existing skips baseline remains visible.

No cases were added or removed, no assertions were weakened, and no skip or explicit flags changed. The original 168 package test attributes and 30 explicit cases remain. Explicit cases were not selected, so this report does not claim they passed. Seven original isolated Python workflows have identical source hashes to HEAD, including Alphalens, Copulae, Copulas, FuzzyCMeans, GPflow, Lingam and PyStan.

## Reproducible environments and actual repairs

The main environment is managed Python 3.11.17 with 491 consistent installed distributions, 493 locked packages and 131 direct third-party requirements. Its ten scientific versions remain unchanged: NumPy 2.4.6, pandas 3.0.6, SciPy 1.17.1, Matplotlib 3.11.2, scikit-learn 1.9.1, statsmodels 0.15.0, PyArrow 26.0.0, Seaborn 0.13.2, Plotly 7.1.0 and TA-lib 0.8.1. Current Darts 0.47.0 installs directly, avoiding u8darts 0.41's older implementation pin, and its original loading and scoring workflow passes.

The existing fixture uses four independently locked optional projects on macOS ARM. Missing environments fail the original cases with installation commands. Child processes use actual managed interpreters, remove inherited Python 3.11 import and library overrides, capture stdout/stderr, check exit 0 and kill owned process trees after 120 seconds. Six existing version cases retain real imports and exact version assertions; PandasTA's current public attribute is `version`.

| Environment | Interpreter | Real workflow and compatible dependencies |
|---|---|---|
| pandas2-tfdf | 3.12.15 | Original Alphalens, Copulae and Copulas workflows; GluonTS 0.17, Pyfolio 0.9.9, Modin 0.37.1 and TFDF 1.12 imports and versions. pandas 2.3.3, NumPy 2.1.3, TensorFlow 2.19.0 and YDF 0.15.0. The inspected YDF 0.16 wheel generates protobuf 6.31.1 messages and fails TensorFlow 2.19's required protobuf 5 runtime; YDF 0.15 generates 5.29.2. No protobuf validation bypass. |
| numpy1-gpflow | 3.12.15 | Original FuzzyCMeans cluster fit/predict, GPflow optimizer/prediction and LiNGAM causal fit; TensorTrade 1.0.4 import/version. NumPy 1.26.4, SciPy 1.13.1, TensorFlow-macos 2.16.2, compatible TFP 0.24 and tf-keras 2.16. Stochastic 0.6 is the newest release declaring Python 3.12 compatibility; 0.7 declares Python <3.11. |
| pandas-ta | 3.13.16 | Genuine PandasTA 0.4.71b0 import/version with Numba 0.61.2, whose interpreter wheels extend through Python 3.13. |
| pystan | 3.14.8 | Latest PyStan 3.14/httpstan 4.17 provides universal ARM/Intel stanc 2.39. The original eight-schools model, four chains, 1,000 samples per chain, eta extraction and DataFrame conversion pass unchanged. The Python 3.15 attempt fails because published httpstan ABIs are cp312/cp313/cp314. The earlier Python 3.11 Intel compiler failure remains historical; a Docker fallback was pulled but was unnecessary. |

All four optional environments pass `uv pip check`: 95, 116, 8 and 25 distributions. The original OR-Tools GLOP workflow remains unchanged in an owned main-Python child because its native Protobuf/HiGHS libraries conflict with TensorFlow/CVXPY in the embedded host. Real Java 25 for H2O, Ipopt for Pyomo, NLTK corpora and current research kernel packages remain installed. Earlier API repairs for Pgmpy's positional DAG operations, DoubleML's dataset namespace, TPOT, LangChain and version fixtures are preserved.

The Hvplot case scopes its backend with `pd.option_context`. Pyomo cleanup removes only newly introduced actual Pyomo deferred-import registry aliases, preserving real modules and the solver; no substitute MPI module is created. `NLTK_DATA` points to the actual `.venv/nltk_data` cache containing punkt, punkt_tab and vader_lexicon.

The paper-order case waits for both real request terminal statuses after all four actual order events, retaining all response, event and status assertions. The data-feed producer acknowledges only after actual aggregation. Its hourly equity delivery wait applies to real exchange-open periods. Original expected SPY 30 minute bars and seven hour bars, plus all other original counts, pass. The invalid closed-market wait at 01:00 New York was diagnosed before correction; expected counts were retained.

## Source and evidence

See the [fixture](../Tests/Python/PythonPackagesTests.cs), [optional projects](../python-environments/README.md), source preservation (workspace maintenance evidence: `lean-optional-source-preservation.json`), final case outcomes (workspace maintenance evidence: `lean-third-party-final-outcomes.json`), unchanged scientific versions (workspace maintenance evidence: `lean-final-core-preservation.json`), consistency (workspace maintenance evidence: `lean-optional-package-consistency.json`), YDF artifact inspection (workspace maintenance evidence: `lean-ydf-protobuf-codegen-compatibility.json`) and genuine ARM Stan workflow/compiler (workspace maintenance evidence: `lean-optional-pystan-original-workflows.json`).

The earlier 124 passed / 14 failed report (workspace maintenance evidence: `lean-python-third-party-report-before-cohorts.md`) remains historical evidence. Its fourteen failed cases were repaired through current Darts and real compatible isolated environments, including current native Stan. Initial constraint history and the 80 passed / 13 failed / 93 reported aborted native coexistence run are retained.

## Exact commands

### final-optional-workflow-tests-build

Exit0, 37.34seconds. Commandrecord (workspace maintenance evidence: `lean-final-optional-workflow-tests-build.json`).

```sh
env PATH=/Users/alex/workspaces/trading/lean/.venv/bin:/Users/alex/.local/share/dotnet:/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin DOTNET_ROOT=/Users/alex/.local/share/dotnet dotnet build Tests/QuantConnect.Tests.csproj --configuration Release --no-restore --verbosity minimal
```

### final-all-optional-workflow-focused-tests

Exit0, 157.06seconds. Commandrecord (workspace maintenance evidence: `lean-final-all-optional-workflow-focused-tests.json`).

```sh
env PATH=/Users/alex/workspaces/trading/lean/.venv/bin:/Users/alex/.local/share/dotnet:/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin PYTHONNET_PYDLL=/Users/alex/.local/share/uv/python/cpython-3.11.17-macos-aarch64-none/lib/libpython3.11.dylib PYTHONPATH=/Users/alex/workspaces/trading/lean/.venv/lib/python3.11/site-packages NLTK_DATA=/Users/alex/workspaces/trading/lean/.venv/nltk_data MPLBACKEND=Agg PYTHONNET_RUNTIME=coreclr DOTNET_ROOT=/Users/alex/.local/share/dotnet DYLD_LIBRARY_PATH=/opt/homebrew/opt/libomp/lib OPENBLAS_NUM_THREADS=2 OMP_NUM_THREADS=2 dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build --filter 'FullyQualifiedName~QuantConnect.Tests.Python.PythonPackagesTests|FullyQualifiedName~HandlesAllTypes|FullyQualifiedName~ProcessesOrdersInLivePaperTrading|FullyQualifiedName~PythonVirtualEnvironmentTests.VirtualEnvironment' --logger 'trx;LogFileName=final-all-optional-workflow-focused.trx' --results-directory TestResults/ayu
```

### final-main-package-consistency

Exit0, 0.32seconds. Commandrecord (workspace maintenance evidence: `lean-final-main-package-consistency.json`).

```sh
uv pip check --python .venv/bin/python
```

### optional-pandas2-tfdf-compatible-lock

Exit0, 0.76seconds. Commandrecord (workspace maintenance evidence: `lean-optional-pandas2-tfdf-compatible-lock.json`).

```sh
uv lock --upgrade
```

### optional-pandas2-tfdf-compatible-sync

Exit0, 0.48seconds. Commandrecord (workspace maintenance evidence: `lean-optional-pandas2-tfdf-compatible-sync.json`).

```sh
uv sync --locked
```

### optional-numpy1-gpflow-compatible-lock

Exit0, 0.25seconds. Commandrecord (workspace maintenance evidence: `lean-optional-numpy1-gpflow-compatible-lock.json`).

```sh
uv lock --upgrade
```

### optional-numpy1-gpflow-compatible-sync

Exit0, 0.21seconds. Commandrecord (workspace maintenance evidence: `lean-optional-numpy1-gpflow-compatible-sync.json`).

```sh
uv sync --locked
```

### optional-numpy1-gpflow-compatible-consistency

Exit0, 0.08seconds. Commandrecord (workspace maintenance evidence: `lean-optional-numpy1-gpflow-compatible-consistency.json`).

```sh
uv pip check --python python-environments/numpy1-gpflow/.venv/bin/python
```

### optional-pandas-ta-lock

Exit0, 0.2seconds. Commandrecord (workspace maintenance evidence: `lean-optional-pandas-ta-lock.json`).

```sh
uv lock --upgrade
```

### optional-pandas-ta-sync

Exit0, 1.85seconds. Commandrecord (workspace maintenance evidence: `lean-optional-pandas-ta-sync.json`).

```sh
uv sync --locked
```

### optional-pystan315-wheel-attempt

Exit1, 1.11seconds. Commandrecord (workspace maintenance evidence: `lean-optional-pystan315-wheel-attempt.json`).

```sh
uv pip compile /tmp/lean-pystan-latest.in --python-version 3.15 --python-platform aarch64-apple-darwin --only-binary :all:
```

### optional-pystan314-lock

Exit0, 0.76seconds. Commandrecord (workspace maintenance evidence: `lean-optional-pystan314-lock.json`).

```sh
uv lock --upgrade
```

### optional-pystan314-sync

Exit0, 18.98seconds. Commandrecord (workspace maintenance evidence: `lean-optional-pystan314-sync.json`).

```sh
uv sync --locked
```

### optional-pystan314-consistency

Exit0, 0.03seconds. Commandrecord (workspace maintenance evidence: `lean-optional-pystan314-consistency.json`).

```sh
uv pip check --python python-environments/pystan/.venv/bin/python
```

