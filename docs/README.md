# LEAN

> **Last Updated**: 2026-04-07T00:00:00Z
> **Git Hash**: `f00d02b`

QuantConnect's LEAN is an institutional-grade, open-source algorithmic trading engine built in C#/.NET. It powers both local backtesting and live trading across equities, futures, options, forex, crypto, CFDs, and alternative data sources. LEAN underpins the QuantConnect cloud platform and is used by professional quant funds worldwide.

## Key Features

| Feature | Description |
|---|---|
| **Multi-Asset Support** | Equities, futures, options, forex, crypto, CFDs, index, index options |
| **Multi-Language Algorithms** | Write strategies in C# or Python |
| **Event-Driven Architecture** | Deterministic, time-sliced event loop for backtest parity with live |
| **Pluggable Handlers** | Swap data feeds, brokerages, result sinks, and schedulers without code changes |
| **Algorithm Framework** | Universe selection, alpha generation, portfolio construction, execution, risk layers |
| **Live Trading** | Interactive Brokers, Alpaca, Binance, Coinbase, Oanda, Tradier, and more |
| **Research Environment** | Jupyter notebooks with full access to historical data and indicators |
| **QuantConnect Integration** | Deploy cloud backtests and live strategies via the LEAN CLI |
| **Indicators Library** | 100+ built-in technical indicators (SMA, EMA, MACD, RSI, Bollinger Bands, …) |
| **Optimization** | Grid-search and Optuna-based hyperparameter optimization out of the box |

## Quick Start (C#)

```csharp
// Algorithm.CSharp/BasicTemplateAlgorithm.cs
using QuantConnect.Data;

namespace QuantConnect.Algorithm.CSharp
{
    public class MyFirstAlgorithm : QCAlgorithm
    {
        private Symbol _spy;

        public override void Initialize()
        {
            SetStartDate(2020, 1, 1);
            SetEndDate(2023, 12, 31);
            SetCash(100_000);

            _spy = AddEquity("SPY", Resolution.Daily).Symbol;
        }

        public override void OnData(Slice slice)
        {
            if (!Portfolio.Invested)
            {
                SetHoldings(_spy, 1.0m);
                Debug("Entered long SPY position");
            }
        }
    }
}
```

Run a local backtest:

```bash
# Build the solution
dotnet build QuantConnect.Lean.sln -c Release

# Run via Launcher (edit Launcher/config.json first)
cd Launcher
dotnet run --project QuantConnect.Lean.Launcher.csproj
```

Or use the LEAN CLI (Docker-based):

```bash
pip install lean
lean project-create MyAlgo --language csharp
lean backtest MyAlgo
lean live MyAlgo
```

## Architecture Summary

LEAN separates concerns into three layers:

1. **System Handlers** — Job queue, messaging, API, logging. Swappable via `config.json`.
2. **Algorithm Handlers** — Data feeds, transaction handler, result sink, real-time scheduler, setup handler.
3. **Algorithm** — User code that extends `QCAlgorithm`. The engine calls lifecycle hooks (`Initialize`, `OnData`, `OnOrderEvent`, etc.).

The `Engine` class wires these layers together. `AlgorithmManager` drives the main time-sliced loop, dispatching data events to the algorithm and collecting orders.

## Documentation Index

| Document | Contents |
|---|---|
| [architecture.md](architecture.md) | System architecture diagram, core components, module overview |
| [workflow.md](workflow.md) | Algorithm execution pipeline, data flow, event lifecycle |
| [state-management.md](state-management.md) | Order state machine, position tracking, portfolio state |
| [development.md](development.md) | Setup, project structure, config reference, troubleshooting |

## Links

- **GitHub**: <https://github.com/QuantConnect/Lean>
- **Documentation**: <https://www.lean.io/docs>
- **QuantConnect Community**: <https://www.quantconnect.com/forum>
- **Docker Hub**: <https://hub.docker.com/u/quantconnect>
- **NuGet Packages**: <https://www.nuget.org/packages?q=QuantConnect>

## Tags

`algorithmic-trading` `backtesting` `live-trading` `csharp` `dotnet` `quantconnect` `event-driven` `multi-asset` `equities` `options` `futures` `forex` `crypto` `portfolio-management` `institutional`
