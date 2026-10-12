# lean: development workflow and layout

Use branch `feature/ayu_develop`. The actual upstream default branch and all fetched branch/tag identities are recorded in the maintenance sync evidence. Merge existing work; do not reset divergent branches or replace conflicting tags.

## Source synchronization

```sh
git fetch origin
git fetch upstream
git switch feature/ayu_develop
git merge origin/feature/ayu_develop
# Merge the actual default branch documented in the sync result.
```

This maintenance used namespaced remote tags before comparing tag identities. Missing upstream tags/branches were pushed to the fork; divergent branches were merged when conflict-free, and conflicting identities were retained for explicit reconciliation. The feature branch contains the default branch plus the maintenance changes. Source snapshot and architecture are documented in [ayu-develop.md](ayu-develop.md).

## Repository layout

- `docs/ayu-develop.md` and `docs/ayu-develop.html`: engineering guide with architecture, workflow, class and sequence diagrams and implementation links.
- `docs/development.md`: toolchain, layout and contributor workflow.
- `docs/validation.md`: exact executed commands, outcomes and limitations.

## Python environment

Use current uv 0.13.0 and a uv-managed interpreter. `.python-version` selects the validated runtime; `uv.lock` records exact resolved dependencies. Python 3.15 was attempted; where native dependency or embedding compatibility requires an earlier interpreter, the evidence explains the chosen fallback.

```sh
UV_PYTHON_PREFERENCE=only-managed uv sync --locked
uv run python --version
```

Libraries and services retain their existing importable source packages and build metadata. Research scripts and .NET-hosted algorithms use `package = false` where installing a synthetic Python library would change their launch/import contracts. Independent framework examples keep independent environments; external Git submodules are not forced into one mutually incompatible Python dependency graph.

LEAN embeds CPython through QuantConnect.pythonnet. Its current native runtime is validated with managed Python3.11 rather than3.15. Run .NET tests with `PYTHONNET_PYDLL` pointing to that managed interpreter's shared library and `PYTHONPATH` pointing to this repository's `.venv/lib/python3.11/site-packages`. This preserves the engine's Python API while making the dependency environment reproducible.

## Verification and documentation

Use the commands and results in [validation.md](validation.md). Markdown is the editable source of the guide; HTML embeds rendered SVG diagrams for offline browsing. Keep actual source links valid after moving modules. Preserve existing cases/assertions while correcting incompatible APIs, genuine resource leaks or invalid fixtures. No additional test cases were requested or added.

## Historical reference

The [previous development guide](archive/development-before-maintenance.md) is retained for source history; use current validation commands for this branch.

## Build and run the .NET engine

The actual application is the C#/.NET solution, with CPython embedded for Python algorithms. The uv project manages the embedded interpreter's packages; it is not a replacement engine or a separately importable Python distribution. The branch selects managed CPython 3.11 because the current `QuantConnect.pythonnet` embedding binary fails on 3.15. The latest compatible scientific core and optional package cohorts are declared in [pyproject.toml](../pyproject.toml) and recorded in [uv.lock](../uv.lock).

```sh
uv sync --locked --group third-party
uv run dotnet build QuantConnect.Lean.sln --configuration Release
```

For embedded tests, set `PYTHONNET_PYDLL` to the uv-managed interpreter's `libpython3.11.dylib` on macOS, and `PYTHONPATH` to `.venv/lib/python3.11/site-packages`. Run the existing NUnit suite through the genuine .NET test project, not a synthetic pytest wrapper:

```sh
uv run --no-sync dotnet test Tests/QuantConnect.Tests.csproj \
  --configuration Release --no-build \
  --logger 'trx;LogFileName=existing-suite.trx' \
  --results-directory TestResults/ayu
```

The [Python package fixture](../Tests/Python/PythonPackagesTests.cs) restores a real Python executable for subprocess workers. On macOS, its Torch, XGBoost and scikit-learn native dependencies require one compatible OpenMP loader: the verified run sets `DYLD_LIBRARY_PATH=/opt/homebrew/opt/libomp/lib`. The existing OR-Tools solver workflow runs in an owned managed-Python subprocess, preserving its original solver assertions while isolating conflicting native protobuf and HiGHS libraries from TensorFlow/CVXPY in the embedded host. The helper captures both streams, asserts the genuine exit status and terminates its owned process tree on timeout; the scientific libraries retain their current compatible versions. Package import success alone is not a functional workflow check. The Python3.11-compatible PyStan wheel embeds an Intel-only Stan compiler; the independently locked current Python3.14 PyStan/httpstan cohort provides a genuine universal ARM compiler. Several other optional libraries require mutually incompatible NumPy/Pandas cohorts. See [validation](validation.md) for exact results and prerequisites.

## Research notebook prerequisites

The supplied [research notebook templates](../Research) execute real Python and .NET kernels. The five existing research checks passed after installing both kernel families in a task-owned Jupyter directory. Install `ipykernel` from the locked group, install the official `Microsoft.dotnet-interactive` tool, and register their kernels without replacing unrelated user kernels. Make the tool directory available on `PATH`. For a tool targeting .NET 8 with the installed .NET 10 host, set `DOTNET_ROLL_FORWARD=Major` and the correct `DOTNET_ROOT`.

The verified local Jupyter search path includes both directories below because the two kernel installers use different prefix conventions:

```sh
export JUPYTER_PATH="$HOME/.local/share/lean-jupyter:$HOME/.local/share/lean-jupyter/share/jupyter"
export PATH="$HOME/.local/share/lean-dotnet-tools:$HOME/.local/share/dotnet:$PATH"
uv run --no-sync dotnet test Tests/QuantConnect.Tests.csproj \
  --configuration Release --no-build \
  --filter 'TestCategory=ResearchRegressionTests'
```

## Compatibility fixes and preserved contracts

Existing Pandas conversion and history cases use current `.map`, `.loc` and `.iloc` APIs while retaining their numeric, index and value assertions. The engine's original rollover order hash remains the expected value: its human-readable timestamp uses an explicit US date format to avoid ICU's new narrow no-break space changing order tags. The existing compression fixture records the observed macOS/Windows ZIP encoding length rather than applying a Linux-specific byte count to another platform.

The [vBase exporter](../Common/Algorithm/Framework/Portfolio/SignalExports/VBaseSignalExport.cs) keeps standardized SHA3-256 collection IDs. It uses the .NET provider when supported and the managed Bouncy Castle 2.7.0 provider otherwise, because macOS does not expose SHA-3 through .NET's native provider. The hash algorithm and exported collection format are unchanged. [Microsoft's platform support table](https://learn.microsoft.com/en-us/dotnet/standard/security/cross-platform-cryptography) and the [official Bouncy Castle release](https://www.bouncycastle.org/download/bouncy-castle-c/) document these prerequisites.

The [previous development guide](archive/development-before-maintenance.md) is retained with a historical banner. The current [engineering guide](ayu-develop.md) contains offline class, sequence and workflow diagrams linked to the owned engine sources.

The existing live universe-transition fixture now uses its existing manual-clock infrastructure during a fixed open-market session and waits for the actual selection event. Its original before/after subscription assertions are retained, with an explicit completed-transition assertion; the supplied case passed in 271 ms. This avoids testing the host weekend or a queued old slice instead of the intended subscription transition.

## Isolated optional scientific workflows

The existing macOS ARM package fixture preserves the current main scientific core and executes incompatible optional workflows in four separately locked uv projects. The original Python source and assertions run in genuine owned child interpreters; the helper checks the real exit status, captures stdout/stderr and kills its process tree after a 120-second bound. It removes inherited Python3.11 import/library overrides and puts the selected child interpreter on PATH. Missing environments fail the existing cases with an installation instruction.

```sh
uv sync --locked --project python-environments/pandas2-tfdf
uv sync --locked --project python-environments/numpy1-gpflow
uv sync --locked --project python-environments/pandas-ta
uv sync --locked --project python-environments/pystan
export NLTK_DATA="$PWD/.venv/nltk_data"
uv run --no-sync python -m nltk.downloader -d "$NLTK_DATA" punkt punkt_tab vader_lexicon
```

[The pandas2/TFDF project](../python-environments/pandas2-tfdf/pyproject.toml) uses managed Python3.12, pandas2.3, TensorFlow2.19 and the latest compatible optional packages. YDF0.15 is the newest inspected wheel whose generated protobuf messages match TensorFlow2.19's required protobuf5 runtime; YDF0.16 generates protobuf6.31.1 messages. [The NumPy1/GPflow project](../python-environments/numpy1-gpflow/pyproject.toml) uses managed Python3.12, NumPy1.26 and SciPy1.13 because current FuzzyCMeans/Lingam require those ranges. Stochastic0.6 is its latest Python3.12-compatible release;0.7 declares Python<3.11. GPflow's macOS ARM TensorFlow2.16 dependency uses compatible TensorFlow Probability0.24 and tf-keras2.16. [The PandasTA project](../python-environments/pandas-ta/pyproject.toml) uses managed Python3.13 because current PandasTA requires Python>=3.12 and its Numba0.61.2 dependency has no newer supported interpreter wheel. [The current Stan project](../python-environments/pystan/pyproject.toml) uses managed Python3.14, PyStan3.14/httpstan4.17 and its bundled universal stanc2.39 compiler. Python3.15 cannot install that httpstan release because its published wheels cover only cp312/cp313/cp314. The original8-schools model, four sampling chains and1000samples run unchanged. Each project retains exact hashes and dependency versions in its own uv.lock. These compatibility boundaries are independent of the engine's Python3.11 embedding ABI.

The original Hvplot workflow scopes its pandas backend option, and the original Pyomo workflow removes only newly introduced actual Pyomo deferred-import registry aliases before later virtual-environment activation. No substitute MPI module is created. The paper-order fixture waits for both genuine request terminal statuses after its four order events. The data-feed fixture acknowledges after actual aggregation and waits for hourly equity bars only during real exchange-open periods; all original expected bar counts remain.


## Final native and Python ownership verification

The final Release solution build passed and the complete default suite ran against source `31ddbce1c1bf7af7fcbaf093f7a539daa7b0bc9c`: 40,341 passed, one genuine CME HTTP-404 integration failure, and 100 existing skips. See [validation](validation.md) for exact selected/discovered counts and [portable commands](maintenance/index.md) for the complete frozen environment.

[PythonWrapper](../Common/Python/PythonWrapper.cs) owns its temporary converted arguments, acquired method reference and discarded return object, releasing each under the GIL. The original memory workload and threshold are unchanged. The [user-defined universe fixture](../Tests/Common/Data/UniverseSelection/UserDefinedUniverseTests.cs) waits on completed real mutations before stopping its worker, rather than assuming host execution speed. All 67 affected existing cases and their corresponding full-suite cases passed.
