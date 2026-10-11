# lean: architecture, features and workflows

Updated: 2026-10-12 · Branch: `feature/ayu_develop` · Source snapshot: `ecb16172f`

[Development and layout](development.md) · [Validation evidence](validation.md) · [HTML edition](ayu-develop.html)

## Purpose and scope

QuantConnect LEAN is a C# algorithmic trading engine with Python algorithm integration. The engine assembles data feeds, algorithm execution, brokerage/transaction handling, real-time scheduling and results processing; an algorithm framework separates universe selection, alpha, portfolio construction, execution and risk management.

## Features

- C# and Python algorithm APIs over a common engine.
- Multi-asset securities, subscriptions and historical data processing.
- Indicators and consolidators for time-series analysis.
- Framework models separate alpha generation from portfolio, execution and risk decisions.
- Backtesting and live handlers share engine interfaces but have distinct data/time/brokerage implementations.

## Components and responsibilities

| Component | Responsibility | Implementation |
| --- | --- | --- |
| Engine coordinator | Assembles and runs algorithm execution handlers | [Engine/Engine.cs](../Engine/Engine.cs) |
| Algorithm API | Provides subscriptions, indicators, orders and portfolio access | [Algorithm/QCAlgorithm.cs](../Algorithm/QCAlgorithm.cs) |
| Algorithm manager | Advances time slices and calls algorithm callbacks | [Engine/AlgorithmManager.cs](../Engine/AlgorithmManager.cs) |
| Transactions | Processes order requests and brokerage fills | [Engine/TransactionHandlers/BrokerageTransactionHandler.cs](../Engine/TransactionHandlers/BrokerageTransactionHandler.cs) |
| Python host | Initializes Python and configures import paths | [Common/Python/PythonInitializer.cs](../Common/Python/PythonInitializer.cs) |

## Architecture and boundaries

The following view shows the principal responsibilities and their data-flow relationship. It is a conceptual architecture view; each implementation link above identifies the owning source. Native or external services retain their own lifecycle and configuration.

```mermaid
flowchart LR
  C0["Engine coordinator"]
  C1["Algorithm API"]
  C2["Algorithm manager"]
  C3["Transactions"]
  C4["Python host"]
  C0 --> C2
  C2 --> C1
  C1 --> C3
  C0 --> C4
  C3 --> C2
```

### Component ownership

The boundaries below distinguish entry points from their state/model and integration responsibilities. Data validation, numerical semantics and external protocol handling belong in the owning modules rather than in documentation or deployment scripts.

```mermaid
flowchart TB
  Caller["Application or operator"]
  Caller --> M0["Engine coordinator"]
  Caller --> M1["Algorithm API"]
  Caller --> M2["Algorithm manager"]
  Caller --> M3["Transactions"]
  Caller --> M4["Python host"]
```

## Types and relationships

This diagram names types present in the linked source. Edges describe the documented relationship; they do not imply additional inheritance.

```mermaid
classDiagram
  class QCAlgorithm
  class Engine
  class AlgorithmManager
  class BrokerageTransactionHandler
  class PythonInitializer
  Engine --> AlgorithmManager : runs
  AlgorithmManager --> QCAlgorithm : dispatches time slices
  QCAlgorithm --> BrokerageTransactionHandler : order requests
  Engine --> PythonInitializer : Python algorithms
```

- [`QCAlgorithm`](../Algorithm/QCAlgorithm.cs)
- [`Engine`](../Engine/Engine.cs)
- [`AlgorithmManager`](../Engine/AlgorithmManager.cs)
- [`BrokerageTransactionHandler`](../Engine/TransactionHandlers/BrokerageTransactionHandler.cs)
- [`PythonInitializer`](../Common/Python/PythonInitializer.cs)

## Runtime workflow

1. Load engine/job configuration and instantiate an algorithm.
2. Initialize subscriptions, securities, portfolio and framework models.
3. Feed synchronized time slices to AlgorithmManager.
4. Apply algorithm decisions through transaction/brokerage handlers.
5. Record fills, portfolio updates, statistics and results.

```mermaid
flowchart TD
  Start(["Start"])
  W0["Load engine/job configuration and instantiate an algorithm."]
  W1["Initialize subscriptions, securities, portfolio and framework models."]
  W2["Feed synchronized time slices to AlgorithmManager."]
  W3["Apply algorithm decisions through transaction/brokerage handlers."]
  W4["Record fills, portfolio updates, statistics and results."]
  Start --> W0
  W0 --> W1
  W1 --> W2
  W2 --> W3
  W3 --> W4
  W4 --> Finish(["Result"])
```

## Sequence of a typical operation

This sequence expands the responsibility flow described above. The named participants correspond to the component table; error handling and any service-specific prerequisites remain part of the implementation.

```mermaid
sequenceDiagram
  actor Operator
  participant P0 as Engine coordinator
  participant P1 as Algorithm API
  participant P2 as Algorithm manager
  participant P3 as Transactions
  participant P4 as Python host
  Operator->>P0: Load algorithm job
  opt Python algorithm
  P0->>P4: Initialize interpreter / import paths
  end
  P0->>P2: Run synchronized time slices
  loop Algorithm execution
  P2->>P1: Invoke data/framework callbacks
  P1->>P3: Submit order requests
  P3-->>P2: Order status / fill events
  end
  P0-->>Operator: Statistics and result records
```

## Development and verification workflow

Sync the upstream refs, merge the actual default branch into `feature/ayu_develop`, resolve conflicts, install the documented environment and run the existing build/test commands. Keep lockfiles and source changes together so another checkout can reproduce the environment.

```mermaid
flowchart LR
  Fetch["Fetch branches and tags"] --> Merge["Merge default into feature branch"]
  Merge --> Install["Install locked toolchain/dependencies"]
  Install --> Build["Compile and build"]
  Build --> Tests["Run existing tests"]
  Tests --> Docs["Verify docs, diagrams and links"]
  Docs --> Commit["Commit reviewed changes"]
```

### Commands

```sh
dotnet build QuantConnect.Lean.sln --configuration Release
dotnet test Tests/QuantConnect.Tests.csproj --configuration Release --no-build
```

The exact commands executed, interpreter/compiler versions, pass/fail counts and any prerequisites are recorded in [validation.md](validation.md). A syntax-only pass is reported separately from a package/native build and from runtime tests.

## Source navigation

These links refer to files in this checkout. They remain useful when the repository is opened locally; the source snapshot above identifies the revision used for this guide.

- [Engine/Engine.cs](../Engine/Engine.cs)
- [Algorithm/QCAlgorithm.cs](../Algorithm/QCAlgorithm.cs)
- [Engine/AlgorithmManager.cs](../Engine/AlgorithmManager.cs)
- [Engine/TransactionHandlers/BrokerageTransactionHandler.cs](../Engine/TransactionHandlers/BrokerageTransactionHandler.cs)
- [Common/Python/PythonInitializer.cs](../Common/Python/PythonInitializer.cs)
- [Algorithm.CSharp/AccordVectorMachinesAlgorithm.cs](../Algorithm.CSharp/AccordVectorMachinesAlgorithm.cs)
- [Algorithm.CSharp/AccumulativeInsightPortfolioRegressionAlgorithm.cs](../Algorithm.CSharp/AccumulativeInsightPortfolioRegressionAlgorithm.cs)
- [Algorithm.CSharp/AddAlphaModelAlgorithm.cs](../Algorithm.CSharp/AddAlphaModelAlgorithm.cs)
- [Algorithm.CSharp/AddAndRemoveOptionContractRegressionAlgorithm.cs](../Algorithm.CSharp/AddAndRemoveOptionContractRegressionAlgorithm.cs)
- [Algorithm.CSharp/AddAndRemoveSecuritySameLoopRegressionAlgorithm.cs](../Algorithm.CSharp/AddAndRemoveSecuritySameLoopRegressionAlgorithm.cs)
- [Algorithm.CSharp/AddBetaIndicatorNewAssetsRegressionAlgorithm.cs](../Algorithm.CSharp/AddBetaIndicatorNewAssetsRegressionAlgorithm.cs)
- [Algorithm.CSharp/AddBetaIndicatorRegressionAlgorithm.cs](../Algorithm.CSharp/AddBetaIndicatorRegressionAlgorithm.cs)
- [Algorithm.CSharp/AddFutureContractWithContinuousRegressionAlgorithm.cs](../Algorithm.CSharp/AddFutureContractWithContinuousRegressionAlgorithm.cs)
- [Algorithm.CSharp/AddFutureOptionContractDataStreamingRegressionAlgorithm.cs](../Algorithm.CSharp/AddFutureOptionContractDataStreamingRegressionAlgorithm.cs)
- [Algorithm.CSharp/AddFutureOptionContractFromFutureChainRegressionAlgorithm.cs](../Algorithm.CSharp/AddFutureOptionContractFromFutureChainRegressionAlgorithm.cs)
