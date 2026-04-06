# LEAN — Development Guide

## Prerequisites

| Tool | Minimum Version | Notes |
|---|---|---|
| .NET SDK | 8.0 | `dotnet --version` to confirm |
| Python | 3.8+ | Required for Python algorithm support |
| Docker | 20.x+ | Required for LEAN CLI workflows |
| Git | 2.x | For submodule management |

Install the .NET SDK: <https://dotnet.microsoft.com/download>

## Project Structure

```
lean/
├── QuantConnect.Lean.sln           # Solution file — build everything from here
├── Launcher/
│   ├── config.json                 # Primary runtime configuration
│   └── Program.cs                  # Entry point — wires handlers and calls Engine.Run()
├── Engine/
│   ├── Engine.cs                   # Core engine — orchestrates all handlers
│   ├── AlgorithmManager.cs         # Main time-sliced event loop
│   ├── DataFeeds/                  # IDataFeed implementations
│   ├── TransactionHandlers/        # Backtest and live order handlers
│   ├── Results/                    # Result writers (JSON, chart data)
│   ├── RealTime/                   # Scheduled event handlers
│   ├── Setup/                      # Algorithm initializers
│   └── HistoricalData/             # History provider implementations
├── Algorithm/
│   ├── QCAlgorithm.cs              # Base algorithm class
│   ├── QCAlgorithm.Trading.cs      # Order placement API
│   ├── QCAlgorithm.Indicators.cs   # Indicator factory methods
│   ├── QCAlgorithm.History.cs      # Historical data access
│   └── QCAlgorithm.Universe.cs     # Universe management
├── Algorithm.Framework/            # Optional 5-layer composable framework
├── Algorithm.CSharp/               # C# example algorithms
├── Algorithm.Python/               # Python example algorithms
├── Common/
│   ├── Orders/                     # Order types, tickets, events, fills
│   ├── Securities/                 # Security models, holdings, portfolio
│   ├── Data/                       # Data types (TradeBar, Tick, Slice, …)
│   ├── Brokerages/                 # Brokerage models (fees, margin, fills)
│   └── Indicators/                 # (see also top-level Indicators/)
├── Brokerages/                     # Live brokerage adapter implementations
├── Indicators/                     # 100+ technical indicator implementations
├── Optimizer/                      # Grid-search and Optuna optimization
├── Research/                       # Jupyter notebook research environment
├── Report/                         # Backtest report generator
├── ToolBox/                        # Data download and conversion tools
├── Tests/                          # Unit and regression tests
└── Data/                           # Sample market data (not in repo — download separately)
```

## Building

```bash
# Clone (or update submodule in this workspace)
cd ext-systems/lean

# Build entire solution (Release)
dotnet build QuantConnect.Lean.sln -c Release

# Build a specific project
dotnet build Engine/QuantConnect.Lean.Engine.csproj -c Release

# Run unit tests
dotnet test Tests/QuantConnect.Tests.csproj

# Run regression tests (requires Data/)
dotnet test Tests/QuantConnect.Tests.csproj --filter "Category=RegressionAlgorithm"
```

## Running a Backtest

1. Edit `Launcher/config.json` to point to your algorithm.
2. Place market data in the `Data/` directory (or use `ToolBox` to download).
3. Run the launcher:

```bash
cd Launcher
dotnet run --project QuantConnect.Lean.Launcher.csproj -c Release
```

## Configuration Reference (`Launcher/config.json`)

Source: [`Launcher/config.json`](../Launcher/config.json)

### Core Settings

| Key | Default | Description |
|---|---|---|
| `environment` | `"backtesting"` | Active environment: `"backtesting"`, `"live-paper"`, `"live-interactive"` |
| `algorithm-type-name` | `"BasicTemplateAlgorithm"` | Fully-qualified class name of the algorithm to run |
| `algorithm-language` | `"CSharp"` | `"CSharp"` or `"Python"` |
| `algorithm-location` | `"QuantConnect.Algorithm.CSharp.dll"` | Path to the compiled DLL or `.py` file |
| `data-folder` | `"../../../Data/"` | Root directory for market data files |
| `debugging` | `false` | Enable debug attach point at startup |
| `debugging-method` | `"LocalCmdline"` | `"LocalCmdline"`, `"VisualStudio"`, `"Debugpy"`, `"PyCharm"` |

### Handler Overrides

| Key | Default Class | Interface |
|---|---|---|
| `log-handler` | `CompositeLogHandler` | `ILogHandler` |
| `messaging-handler` | `Messaging` | `IMessagingHandler` |
| `job-queue-handler` | `JobQueue` | `IJobQueueHandler` |
| `api-handler` | `Api` | `IApi` |
| `data-provider` | `DefaultDataProvider` | `IDataProvider` |
| `data-aggregator` | `AggregationManager` | `IDataAggregator` |
| `object-store` | `LocalObjectStore` | `IObjectStore` |

### Data & Symbol Limits

| Key | Default | Description |
|---|---|---|
| `symbol-minute-limit` | `10000` | Max symbols at minute resolution |
| `symbol-second-limit` | `10000` | Max symbols at second resolution |
| `symbol-tick-limit` | `10000` | Max symbols at tick resolution |
| `maximum-data-points-per-chart-series` | `1000000` | Chart rendering limit |
| `maximum-chart-series` | `30` | Max chart series per backtest |

### Live Brokerage Settings (Interactive Brokers example)

```json
"ib-account": "",
"ib-user-name": "",
"ib-password": "",
"ib-host": "127.0.0.1",
"ib-port": "4002",
"ib-trading-mode": "paper"
```

Other brokerages (Alpaca, Oanda, Tradier, Binance, Coinbase) have analogous key groups in `config.json`.

### Environment Layering

`config.json` supports named environment blocks that overlay the top-level keys. Example:

```json
"environment": "live-paper",
"environments": {
  "live-paper": {
    "live-mode": true,
    "setup-handler": "QuantConnect.Lean.Engine.Setup.BrokerageSetupHandler",
    "data-feed-handler": "QuantConnect.Lean.Engine.DataFeeds.LiveTradingDataFeed",
    "transaction-handler": "QuantConnect.Lean.Engine.TransactionHandlers.BrokerageTransactionHandler"
  }
}
```

## Writing an Algorithm

### C# Algorithm Skeleton

```csharp
using QuantConnect;
using QuantConnect.Algorithm;
using QuantConnect.Data;

public class MyAlgorithm : QCAlgorithm
{
    public override void Initialize()
    {
        SetStartDate(2022, 1, 1);
        SetEndDate(2023, 12, 31);
        SetCash(100_000);

        AddEquity("AAPL", Resolution.Daily);
        AddEquity("MSFT", Resolution.Daily);
    }

    public override void OnData(Slice slice)
    {
        if (!Portfolio.Invested)
        {
            SetHoldings("AAPL", 0.5m);
            SetHoldings("MSFT", 0.5m);
        }
    }

    public override void OnOrderEvent(OrderEvent orderEvent)
    {
        Log($"Order {orderEvent.OrderId}: {orderEvent.Status} @ {orderEvent.FillPrice}");
    }
}
```

### Python Algorithm Skeleton

```python
from AlgorithmImports import *

class MyAlgorithm(QCAlgorithm):
    def initialize(self):
        self.set_start_date(2022, 1, 1)
        self.set_end_date(2023, 12, 31)
        self.set_cash(100_000)
        self.aapl = self.add_equity("AAPL", Resolution.DAILY).symbol

    def on_data(self, slice: Slice):
        if not self.portfolio.invested:
            self.set_holdings(self.aapl, 1.0)

    def on_order_event(self, order_event: OrderEvent):
        self.log(f"Order {order_event.order_id}: {order_event.status}")
```

## Troubleshooting

### 1. `Could not find algorithm type`

**Symptom**: Engine throws `AlgorithmTypeNotFoundException` at startup.

**Cause**: The `algorithm-type-name` in `config.json` does not match the compiled class name, or `algorithm-location` points to a missing or stale DLL.

**Fix**: Rebuild the solution (`dotnet build`) and confirm the DLL path. Use the fully-qualified type name including namespace if ambiguous.

---

### 2. `Data not found` / Empty Slice

**Symptom**: `OnData` receives empty slices; no trades execute.

**Cause**: The `data-folder` path does not contain data files for the requested ticker, resolution, and date range.

**Fix**: Download data using `ToolBox` or the QuantConnect cloud data downloader. Check the expected file path format: `Data/equity/usa/minute/spy/20200101_trade.zip`.

---

### 3. `Margin model rejected order` / Orders not filling

**Symptom**: Orders appear with `OrderStatus.Invalid` and a margin error message.

**Cause**: The algorithm requests more buying power than the portfolio has (including margin). Default equity margin is 2× for day trades, 1× for overnight.

**Fix**: Reduce position size. Check `Portfolio.MarginRemaining` before placing orders. Override the brokerage model's margin model if needed.

---

### 4. Python algorithm fails to load (`PythonEngine` errors)

**Symptom**: `pythonnet` or `Python.Runtime` exceptions at startup when using a Python algorithm.

**Cause**: Python is not installed, the virtual environment is not activated, or `python-venv` in `config.json` is misconfigured.

**Fix**: Install Python 3.8+, create a venv, install `lean` and `quantconnect` packages, and set `"python-venv": "/path/to/venv"` in `config.json`. On Linux, ensure `libpython3.x.so` is on `LD_LIBRARY_PATH`.

---

### 5. `OutOfMemoryException` during large backtests

**Symptom**: Engine crashes during backtesting many symbols over a long date range.

**Cause**: Tick or second resolution data for many symbols generates large in-memory queues.

**Fix**:
- Reduce resolution to `Minute` or `Daily` where possible.
- Lower `symbol-minute-limit` / `symbol-tick-limit` in `config.json`.
- Enable `"maximum-data-points-per-chart-series"` cap.
- Run via Docker with `--memory` limit to surface the issue early.

---

### 6. Live trading orders not appearing in brokerage

**Symptom**: Algorithm places orders but they are not visible in the brokerage dashboard.

**Cause**: Brokerage API credentials missing or incorrect; wrong environment selected (paper vs. live).

**Fix**: Verify credentials in `config.json`. Ensure `ib-trading-mode` (or equivalent) is set to `"live"` (not `"paper"`) and that TWS / Gateway is running and connected. Check LEAN logs for brokerage error messages.

---

### 7. Scheduled events not firing

**Symptom**: `Schedule.On(...)` callbacks never execute.

**Cause**: The scheduled time falls outside market hours, or `force-exchange-always-open` is `false` (default) and the market is closed.

**Fix**: Use `DateRules.EveryDay` + `TimeRules.At(9, 30)` relative to market open, or set `"force-exchange-always-open": true` in `config.json` for testing.

## Security Considerations

- **API credentials** in `config.json` (`api-access-token`, brokerage passwords) are stored in plaintext. Use environment variable substitution or a secrets manager in production.
- **Live trading** should always be tested in paper mode first. Set `"ib-trading-mode": "paper"` or the brokerage equivalent.
- The `job-user-id` / `api-access-token` fields grant access to the QuantConnect cloud API — rotate these tokens regularly.
- When running LEAN in Docker, bind-mount `config.json` from a secrets vault rather than baking credentials into the image.
- Restrict file system access: the `data-folder` should be read-only for the LEAN process in production.

## See Also

- [architecture.md](architecture.md) — Handler interfaces and plugin points
- [workflow.md](workflow.md) — Algorithm execution pipeline
- [state-management.md](state-management.md) — Order and portfolio state
