# lean: maintenance validation

Status: work in progress until the final recorded checks and source commit are reviewed. Failed exploratory commands remain here as evidence; later successful commands do not make an unexecuted full suite pass.

## Executed checks

| Check | Exit | Duration | Command | Evidence |
| --- | ---: | ---: | --- | --- |
| build | 0 | 113.2s | `dotnet build QuantConnect.Lean.sln --configuration Release --nologo` | [lean-build.log](../../docs/maintenance-2026-10-12/lean-build.log) |
| embedded311-indicator-tests | 0 | 12.3s | `dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build --filter FullyQualifiedName~IndicatorTests --logger trx;LogFileName=indicator311.trx --results-directory TestResults/ayu` | [lean-embedded311-indicator-tests.log](../../docs/maintenance-2026-10-12/lean-embedded311-indicator-tests.log) |
| python311-tests | 1 | 728.5s | `dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build --logger trx;LogFileName=python311.trx --results-directory TestResults/ayu` | [lean-python311-tests.log](../../docs/maintenance-2026-10-12/lean-python311-tests.log) |
| python315-test-attempt | 1 | 11.5s | `dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build --filter FullyQualifiedName~IndicatorTests --logger trx;LogFileName=python315.trx --results-directory TestResults/ayu` | [lean-python315-test-attempt.log](../../docs/maintenance-2026-10-12/lean-python315-test-attempt.log) |
| test | 1 | 5.7s | `dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build --logger trx;LogFileName=ayu-existing-tests.trx --results-directory TestResults/ayu` | [lean-test.log](../../docs/maintenance-2026-10-12/lean-test.log) |
| uv311-latest-sync | 0 | 4.7s | `uv sync --upgrade --python 3.11` | [lean-uv311-latest-sync.log](../../docs/maintenance-2026-10-12/lean-uv311-latest-sync.log) |

## Scope and limitations

Build, package import, syntax compilation, unit tests and external integration tests are distinct checks. Live venue tests require their documented services/data/credentials. An interpreter fallback records a concrete installation/build attempt rather than assuming wheel absence means the interpreter is impossible. No tests are reported as passed merely because compilation or collection succeeded.

The final workspace report links source commits, complete outcomes and remaining requirements. [Architecture and workflows](ayu-develop.md) · [Development layout](development.md)

## Final owned Python package and asynchronous fixture validation

The final Release Tests build passed with zero errors in 37.34 seconds. All 170 selected existing cases passed in 157.06 seconds with zero failures or skips, including all 138 non-explicit package cases, 30 data-feed cases, paper-order and virtual-environment activation. The current main environment's 491 distributions and four real optional cohorts are consistent; ten scientific versions are unchanged. The previous complete 40,442-case baseline had 40,322 passed, 20 failed and 100 existing skips. It precedes these last repairs; a fresh complete-suite result is recorded separately by the parent.

Exact commands, API details, interpreter fallbacks and limitations are in [Python package workflows](python-packages.md) and [development](development.md). No additional test cases or weakened assertions were introduced.
