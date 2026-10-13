# lean: maintenance validation

<!-- current-documentation-provenance -->
Documentation reviewed: 2026-10-13 / Git source base: `76d004a671cfbe9d0f0e21f8fb2e3b28ea668aaf` / [Current architecture and feature guide](ayu-develop.md).
Historical execution commands/results below retain their original dates and scope; this metadata update is not a new application test run.
<!-- /current-documentation-provenance -->



Current user-authorized test policy: The user explicitly disabled `LiveOptionChainProviderReturnsFutureOptionData` with NUnit Ignore because its live CME endpoint returns HTTP404. The original test body and assertion remain unchanged. The retained full-suite baseline is 40,341 passes and 100 existing skips; verification of the new single skip is separate.

The final complete default suite at source commit `31ddbce1c1bf7af7fcbaf093f7a539daa7b0bc9c` finished with **40,341 passed, one failed and 100 pre-existing skips** (40,442 selected cases), exit 1. All internal cases selected by this run passed. The sole failure is `LiveOptionChainProviderReturnsFutureOptionData`: CME returns HTTP 404 for the real live option-chain quote endpoint. The original assertion is retained; the full suite is not claimed green.

The final Release solution build passed with zero errors in 61.97 seconds. The full test command took 1,029.70 seconds wall time (the runner reports 16 minutes 56 seconds). TRX discovery contains 40,893 cases; 40,342 executed, while 451 pre-existing explicit cases were not selected and 100 existing skips remain. Discovery, execution and default selection are distinct counts.

[Portable command evidence](maintenance/index.md) includes exact environment settings, source snapshots and original-log hashes. Earlier failed or interrupted commands remain visible.

## Executed checks

| Check | Exit | Duration | Command | Evidence |
| --- | ---: | ---: | --- | --- |
| build | 0 | 113.2s | `dotnet build QuantConnect.Lean.sln --configuration Release --nologo` | [check record](maintenance/index.html) |
| embedded311-indicator-tests | 0 | 12.3s | `dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build --filter FullyQualifiedName~IndicatorTests --logger trx;LogFileName=indicator311.trx --results-directory TestResults/ayu` | [check record](maintenance/index.html) |
| python311-tests | 1 | 728.5s | `dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build --logger trx;LogFileName=python311.trx --results-directory TestResults/ayu` | [check record](maintenance/index.html) |
| python315-test-attempt | 1 | 11.5s | `dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build --filter FullyQualifiedName~IndicatorTests --logger trx;LogFileName=python315.trx --results-directory TestResults/ayu` | [check record](maintenance/index.html) |
| test | 1 | 5.7s | `dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build --logger trx;LogFileName=ayu-existing-tests.trx --results-directory TestResults/ayu` | [check record](maintenance/index.html) |
| uv311-latest-sync | 0 | 4.7s | `uv sync --upgrade --python 3.11` | [check record](maintenance/index.html) |

## Scope and limitations

Build, package import, syntax compilation, unit tests and external integration tests are distinct checks. Live venue tests require their documented services/data/credentials. An interpreter fallback records a concrete installation/build attempt rather than assuming wheel absence means the interpreter is impossible. No tests are reported as passed merely because compilation or collection succeeded.

The final workspace report links source commits, complete outcomes and remaining requirements. [Architecture and workflows](ayu-develop.md) · [Development layout](development.md)

## Final owned Python package and asynchronous fixture validation

The final Release Tests build passed with zero errors in 37.34 seconds. All 170 selected existing cases passed in 157.06 seconds with zero failures or skips, including all 138 non-explicit package cases, 30 data-feed cases, paper-order and virtual-environment activation. The current main environment's 491 distributions and four real optional cohorts are consistent; ten scientific versions are unchanged. The previous complete 40,442-case baseline had 40,322 passed, 20 failed and 100 existing skips. It precedes these last repairs; a fresh complete-suite result is recorded separately by the parent.

Exact commands, API details, interpreter fallbacks and limitations are in [Python package workflows](python-packages.md) and [development](development.md). No additional test cases or weakened assertions were introduced.

## Final reference ownership and universe-thread verification

The [Python method wrapper](../Common/Python/PythonWrapper.cs) now disposes newly owned converted argument references even when conversion fails, disposes acquired method references, and releases discarded Python return values under the GIL. Caller-owned Python objects are not disposed. This fixes the original long-running fee-wrapper memory assertion without changing its 50,000-call workload or five-percent threshold.

The existing [user-defined universe fixture](../Tests/Common/Data/UniverseSelection/UserDefinedUniverseTests.cs) resets its mutation counter and waits for the real worker to finish at least 50 mutations before cancellation. It explicitly detects a worker that fails to stop and disposes only its own synchronization resources. The original mutation count, completed-run and order assertions remain; no synthetic events replace worker execution.

All 67 affected existing wrapper, fee, memory and universe cases passed in the focused post-build check. The subsequent complete suite also passed those cases; the live CME endpoint is its only failure. No additional test cases were added.

## Remaining live CME requirement

The [live chain provider](../Engine/DataFeeds/LiveOptionChainProvider.cs) successfully reaches product and expiration metadata but the option quote roster URL responds 404. Its five actual retries and original nonempty-chain assertion are preserved. The official [CME E-mini S&P 500 option page](https://www.cmegroup.com/markets/equities/sp/e-mini-sandp500.quotes.options.html) remains the source for investigating a current replacement. Fabricated strikes, a different venue or a skipped assertion would not verify this requirement.


A bounded follow-up confirmed an explicit CME IP restriction: the response states that automated access is blocked and directs users to approved delivery methods. Requests stopped after this response. The official [Reference Data API v3](https://cmegroupclientsite.atlassian.net/wiki/spaces/EPICSANDBOX/pages/457215780/CME+Reference+Data+API+Version+3) documents an authenticated same-venue path; its [option-series endpoint](https://cmegroupclientsite.atlassian.net/wiki/spaces/EPICSANDBOX/pages/647364612/CME+Reference+Data+API+-+Option+Series+Endpoint) can identify active instruments by series. It requires registered OAuth credentials. No authenticated ES roster was verified, so this is a documented future integration path rather than a completed endpoint migration.

[Read-only replacement audit](maintenance/cme-official-replacement-audit.json) and [exact block response](maintenance/cme-expirations-response.json) preserve request metadata and the limitation. This remains an external prerequisite for the original live case.

Authorized-skip verification completed successfully: 1 selected original case(s) skipped, zero failures, 56.07 wall seconds. Exact command and terminal result are included in `maintenance/verification.json`; the earlier full-suite counts remain historical baseline evidence. Independent review verified the original test bodies and assertions are unchanged.
