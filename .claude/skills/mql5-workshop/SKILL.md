---
name: mql5-workshop
description: "Full MQL5 Reference curriculum aligned to https://www.mql5.com/en/docs (zh/docs). Covers language basics, events, trading (order/deal/position), timeseries, charts/objects, files, network, Standard Library, testing, indicators, panel EAs, compilation. ALWAYS treat official docs as source of truth; use this skill as the map + critical rules. Use for ANY MQL5 coding, debugging, compiling, retcode research."
---

# MQL5 Workshop (Official Docs Curriculum)

**Source of truth**: [MQL5 Reference (EN)](https://www.mql5.com/en/docs) · [中文](https://www.mql5.com/zh/docs)

**Agent rule**: When writing or reviewing MQL5, verify signatures, enums, and retcodes against the official Reference. This skill is the **complete curriculum map** of that documentation plus battle-tested patterns. Do **not** invent APIs. If unsure, open the linked doc page for that topic.

---

## Quick Navigation

| # | Section | Official root |
|---|---------|----------------|
| 0 | [How to use this skill](#0-how-to-use-this-skill) | — |
| 1 | [Official Reference Atlas](#1-official-reference-atlas) | [/en/docs](https://www.mql5.com/en/docs) |
| 2 | [Language Basics](#2-language-basics) | [/en/docs/basis](https://www.mql5.com/en/docs/basis) |
| 3 | [Applications & Events](#3-applications--events) | [/en/docs/event_handlers](https://www.mql5.com/en/docs/event_handlers) |
| 4 | [Trading Model](#4-trading-model) | [/en/docs/trading](https://www.mql5.com/en/docs/trading) |
| 5 | [Market / Account / Terminal](#5-market--account--terminal) | [/en/docs/marketinfo](https://www.mql5.com/en/docs/marketinfo) |
| 6 | [Timeseries & Indicators Access](#6-timeseries--indicators-access) | [/en/docs/series](https://www.mql5.com/en/docs/series) |
| 7 | [Custom Indicators](#7-custom-indicators) | [/en/docs/customind](https://www.mql5.com/en/docs/customind) |
| 7b | [OnCalculate deep dive](#7b-oncalculate-deep-dive) | [/event_handlers/oncalculate](https://www.mql5.com/en/docs/event_handlers/oncalculate) |
| 8 | [Charts, Objects, UI](#8-charts-objects-ui) | [/en/docs/chart_operations](https://www.mql5.com/en/docs/chart_operations) |
| 8b | [Chart events deep dive](#8b-chart-events-deep-dive) | [/enum_chartevents](https://www.mql5.com/en/docs/constants/chartconstants/enum_chartevents) |
| 8c | [Economic Calendar](#8c-economic-calendar) | [/en/docs/calendar](https://www.mql5.com/en/docs/calendar) |
| 9 | [Files, Strings, Math, DateTime](#9-files-strings-math-datetime) | [/en/docs/files](https://www.mql5.com/en/docs/files) |
| 10 | [Network / WebRequest](#10-network--webrequest) | [/en/docs/network](https://www.mql5.com/en/docs/network) |
| 11 | [SQLite / Matrix / OpenCL / Python](#11-sqlite--matrix--opencl--python) | [/en/docs/database](https://www.mql5.com/en/docs/database) |
| 12 | [Standard Library](#12-standard-library) | [/en/docs/standardlibrary](https://www.mql5.com/en/docs/standardlibrary) |
| 13 | [Testing & Optimization](#13-testing--optimization) | [/en/docs/optimization_types](https://www.mql5.com/en/docs/optimization_types) |
| 14 | [Panel EA Patterns](#14-panel-ea-patterns) | Controls + Chart events |
| 15 | [Compilation](#15-compilation) | MetaEditor |
| 16 | [Docs Research Workflow](#16-docs-research-workflow) | — |
| 17 | [Quick Reference](#17-quick-reference) | — |

---

## 0. How to use this skill

1. Identify the task domain (trade / indicator / UI / file / network…).
2. Jump to that section here for critical rules.
3. Open the linked official page before finalizing code.
4. Prefer Standard Library (`Include\Trade\Trade.mqh`, `Controls\…`) when it covers the need.
5. Log `retcode`, `comment`, and relevant `SymbolInfo*` / `AccountInfo*` properties on trade failures.

---

## 1. Official Reference Atlas

Top-level map of [https://www.mql5.com/en/docs](https://www.mql5.com/en/docs) (agent must be able to navigate all of these):

| Category | URL | What you must know |
|----------|-----|--------------------|
| Language Basics | [/basis](https://www.mql5.com/en/docs/basis) | Types, OOP, preprocessor, namespaces |
| MQL5 programs | [/runtime](https://www.mql5.com/en/docs/runtime) | Program properties, running context |
| Predefined Variables | [/predefined](https://www.mql5.com/en/docs/predefined) | `_Symbol`, `_Period`, `_Point`, `_Digits`, `_LastError`… |
| Common Functions | [/common](https://www.mql5.com/en/docs/common) | `Print`, `Alert`, `Sleep`, `GetLastError`, `TerminalInfo*` |
| Array Functions | [/array](https://www.mql5.com/en/docs/array) | `ArraySetAsSeries`, `ArrayResize`, `ArrayCopy` |
| Matrix & Vector | [/matrix](https://www.mql5.com/en/docs/matrix) | Native linear algebra |
| Conversion | [/convert](https://www.mql5.com/en/docs/convert) | `StringToTime`, `CharArrayToString`, `NormalizeDouble` |
| Math | [/math](https://www.mql5.com/en/docs/math) | `Math*` |
| Date and Time | [/dateandtime](https://www.mql5.com/en/docs/dateandtime) | `TimeCurrent`, `TimeGMT`, `StructToTime` |
| Account | [/account](https://www.mql5.com/en/docs/account) | Balance, margin mode, FIFO |
| Trade Functions | [/trading](https://www.mql5.com/en/docs/trading) | Order / deal / position API |
| Trade Signals | [/signals](https://www.mql5.com/en/docs/signals) | Signal subscription APIs |
| Chart Operations | [/chart_operations](https://www.mql5.com/en/docs/chart_operations) | ChartID, redraw, navigate |
| Chart Constants | [/constants/chartconstants](https://www.mql5.com/en/docs/constants/chartconstants) | Chart properties / events |
| Objects | [/objects](https://www.mql5.com/en/docs/objects) | `ObjectCreate`, OBJ_* |
| Economic Calendar | [/calendar](https://www.mql5.com/en/docs/calendar) | `CalendarValueHistory` etc. |
| Market Info | [/marketinfo](https://www.mql5.com/en/docs/marketinfo) | Symbol selects, ticks, depth |
| Timeseries | [/series](https://www.mql5.com/en/docs/series) | `CopyRates`, `CopyBuffer`, `iClose`… |
| Custom Indicators | [/customind](https://www.mql5.com/en/docs/customind) | Buffers, styles, `Indicator*` |
| Indicators (built-in) | [/indicators](https://www.mql5.com/en/docs/indicators) | `iMA`, `iATR`, handles |
| Event Handling | [/event_handlers](https://www.mql5.com/en/docs/event_handlers) | OnTick, OnCalculate, OnChartEvent… |
| Level2 / Book | [/marketinfo/marketbookadd](https://www.mql5.com/en/docs/marketinfo/marketbookadd) | DOM |
| Files | [/files](https://www.mql5.com/en/docs/files) | `FileOpen` flags, common folder |
| Strings | [/strings](https://www.mql5.com/en/docs/strings) | `StringFormat`, split, find |
| Global Variables | [/globals](https://www.mql5.com/en/docs/globals) | Terminal global vars |
| Database (SQLite) | [/database](https://www.mql5.com/en/docs/database) | `DatabaseOpen`… |
| Network | [/network](https://www.mql5.com/en/docs/network) | `WebRequest`, sockets |
| OpenCL | [/opencl](https://www.mql5.com/en/docs/opencl) | GPU compute |
| Python Integration | [/python](https://www.mql5.com/en/docs/python_metatrader5) | `MetaTrader5` package |
| Standard Library | [/standardlibrary](https://www.mql5.com/en/docs/standardlibrary) | Trade, Controls, Arrays… |
| Constants / Enums | [/constants](https://www.mql5.com/en/docs/constants) | Retcodes, order types, filling |
| Errors | [/constants/errorswarnings](https://www.mql5.com/en/docs/constants/errorswarnings) | Runtime + trade retcodes |
| Testing | [/optimization_types](https://www.mql5.com/en/docs/optimization_types) | Tester modes |

---

## 2. Language Basics

Docs: [Language Basics](https://www.mql5.com/en/docs/basis)

Sections to master: Syntax · Data Types · Operations · Operators · Functions · Variables · Preprocessor · OOP · Namespaces.

Critical rules:

- MQL5 ≈ C++ subset + trading/event extensions (enumerations, structures, classes, event handlers).
- Strict typing; prefer `datetime`, `color`, `enumerations` over magic ints.
- `input` variables are external parameters (EA/indicator/script inputs).
- `#property` sets program metadata (strict, copyright, indicator buffers/plots, script show confirm…).
- `#include <file.mqh>` → `MQL5/Include`; `#include "file.mqh"` → beside the source.
- OOP: classes, inheritance, virtual methods; Standard Library is OOP-first.
- Dynamic arrays: `ArrayResize`; series indexing via `ArraySetAsSeries`.
- `NormalizeDouble` for display/digits; for trade prices also align to `SYMBOL_TRADE_TICK_SIZE`.

### Types (official)

Docs: [Data Types](https://www.mql5.com/en/docs/basis/types)

| Kind | Types | Notes |
|------|-------|-------|
| Integers | `char`…`ulong` | Fastest |
| Bool / literals | `bool`, `ushort` char codes | |
| Floating | `double`, `float` | Prices are `double` (no dedicated price type) |
| Special ints | `color`, `datetime` | Convenience for Inputs UI; stored as integers |
| Strings | `string` | Slowest (dynamic alloc) |
| Complex | `struct`, `class` | Abstract / OOP types |

Expressions use **implicit** casting unless explicit cast is written. Prefer enums over raw ints for API fields.

### Predefined variables

Docs: [Predefined](https://www.mql5.com/en/docs/predefined) — set by terminal; mostly read-only (`_LastError` cleared via `ResetLastError`).

| Var | Meaning |
|-----|---------|
| `_Symbol` / `_Period` | Chart symbol / TF |
| `_Digits` / `_Point` | Quote digits / point size |
| `_LastError` | Last error |
| `_StopFlag` | Stop requested |
| `_UninitReason` | Deinit reason |
| `_AppliedTo` | Indicator apply-to data type |
| `_IsX64` | 64-bit terminal |
| `_RandomSeed` | PRNG state |

Not defined inside libraries — libraries see the caller’s values.

### Arrays

Docs: [Array Functions](https://www.mql5.com/en/docs/array)

- Max **4** dimensions; index `0 … size-1`.
- Series flag: `ArraySetAsSeries` / `ArrayGetAsSeries` / `ArrayIsSeries` (timeseries vs buffer).
- Hot APIs: `ArrayResize`, `ArrayCopy`, `ArrayFree`, `ArrayInitialize`/`Fill`, `Maximum`/`Minimum`, `Sort`, `Bsearch`, `Insert`/`Remove`/`Reverse`/`Swap`.
- Prefer static/preallocated buffers in `OnTick`/`OnCalculate`; avoid thrashing `ArrayResize` every tick.

### Common helpers

Docs: [Common Functions](https://www.mql5.com/en/docs/common)

| Need | Call |
|------|------|
| Log | `Print` / `PrintFormat` |
| Chart text | `Comment` (top-left; easy to overwrite) |
| Stop EA | `ExpertRemove` |
| Yield | `Sleep` (EA/script; not in indicators’ calc path) |
| Timing | `GetTickCount(64)`, `GetMicrosecondCount` |
| Errors | `ResetLastError`, `SetUserError` |
| Tester $ | `TesterDeposit` / `Withdrawal` / `Stop` / `Statistics` |
| Resources | `ResourceCreate` / `Free` / `ReadImage` / `Save` |

---

## 3. Applications & Events

### Application types

| Type | Chart-bound | Handlers | Folder |
|------|-------------|----------|--------|
| Expert Advisor | Yes | Init/Deinit/Tick/Timer/Trade/TradeTransaction/ChartEvent/Book/Tester* | `Experts` |
| Indicator | Yes | Init/Deinit/Calculate (+ Timer/ChartEvent) — **cannot trade** | `Indicators` |
| Script | Yes | `OnStart` only | `Scripts` |
| Service | **No** | `OnStart`; can `EventChartCustom` | `Services` |
| Library | — | No events | `Libraries` |
| Include | — | Compiled in | `Include` |

Docs: [MQL5 Programs / Runtime](https://www.mql5.com/en/docs/runtime)

- Compile → `.ex5` in Experts / Indicators / Scripts (must be error-free).
- Chart limit: **1 EA + 1 script + unlimited indicators**.
- EA/indicator stay across terminal restarts; **scripts do not** (run once, then removed).
- Remove EA via Experts list; AutoTrading button also gates live trading.
- Services: no chart bind — custom symbols, network loops, helpers.

### Event handling

Docs: [Event Handling](https://www.mql5.com/en/docs/event_handlers)

- Handler name + signature must match docs exactly.
- Events processed sequentially; avoid infinite loops in EA/indicator.
- Duplicate `NewTick` / `ChartEvent` / `Timer` while one is queued/handling → **dropped**.
- Libraries do not receive events.

| Handler | Use |
|---------|-----|
| `OnInit` / `OnDeinit` | Construct / release handles, timers, objects |
| `OnTick` | EA per-quote logic |
| `OnCalculate` | Indicator calc (two call forms: OHLC arrays or rates_total/prev_calculated) |
| `OnTimer` | UI refresh, trailing, multi-TP checks |
| `OnTrade` | After trade operations complete |
| `OnTradeTransaction` | Fine-grained transaction stream (preferred for robust EA state) |
| `OnChartEvent` | Clicks, object drag, keys, custom events |
| `OnBookEvent` | Depth of Market |
| `OnTester*` | Optimization / forward testing hooks |

### OnInit return codes

Docs: [OnInit](https://www.mql5.com/en/docs/event_handlers/oninit)

Prefer `int OnInit()` (void form is legacy).

| Code | Effect |
|------|--------|
| `INIT_SUCCEEDED` (0) | OK |
| `INIT_FAILED` | EA unloaded; indicator stays but **handlers stop**. Tester reloads EA next pass (slow) |
| `INIT_PARAMETERS_INCORRECT` | Skip this input set in optimization (red row); agent gets new task |
| `INIT_AGENT_NOT_SUITABLE` | Agent banned for rest of this optimization (RAM/OpenCL/…) |

Bad params → `INIT_PARAMETERS_INCORRECT`. Missing indicator handle → `INIT_FAILED`. Prefer `TesterStop()` mid-test over `INIT_FAILED` when possible.

### Timers & custom events

Docs: [Event Functions](https://www.mql5.com/en/docs/eventfunctions)

| Call | Role |
|------|------|
| `EventSetTimer(seconds)` | Second-resolution `OnTimer` |
| `EventSetMillisecondTimer(ms)` | Sub-second `OnTimer` |
| `EventKillTimer()` | Stop (call in `OnDeinit`) |
| `EventChartCustom(chart_id, id, lparam, dparam, sparam)` | Fire `CHARTEVENT_CUSTOM+N` |

One timer per program; recreate after chartchange. Panel UIs often refresh on timer + chart events.

`OnTimer` notes ([docs](https://www.mql5.com/en/docs/event_handlers/ontimer)):

- Kill in `OnDeinit` (else terminal force-kills on unload).
- Duplicate Timer while one is queued/processing → **dropped** (same as Tick/ChartEvent).
- Live hardware floor ~10–16 ms between timer events; tester slows if period too small.

---

## 4. Trading Model

Docs: [Trade Functions](https://www.mql5.com/en/docs/trading)

### Order · Deal · Position

- **Order**: instruction to the broker (market or pending; also SL/TP levels conceptually).
- **Deal**: executed exchange. Buy at **Ask**, Sell at **Bid**. One order may produce multiple deals.
- **Position**: net (or hedged) exposure in a symbol.

Trading functions: **EA + Script only**, and only if **Allow live trading** is enabled. See Trade Permission rules in docs.

### Core API groups

| Group | Functions |
|-------|-----------|
| Check / send | `OrderCheck`, `OrderCalcMargin`, `OrderCalcProfit`, `OrderSend`, `OrderSendAsync` |
| Positions | `PositionsTotal`, `PositionGetTicket`, `PositionSelect*`, `PositionGetDouble/Integer/String` |
| Open orders | `OrdersTotal`, `OrderGetTicket`, `OrderSelect`, `OrderGet*` |
| History | `HistorySelect*`, `HistoryOrdersTotal`, `HistoryDealsTotal`, `HistoryDealGet*`, `HistoryOrderGet*` |

Structures: [`MqlTradeRequest`](https://www.mql5.com/en/docs/constants/structures/mqltraderequest), `MqlTradeResult`, `MqlTradeCheckResult`.

### Filling & execution (must get right)

Docs: [Symbol Properties – filling](https://www.mql5.com/en/docs/constants/environment_state/marketinfoconstants)

- `SYMBOL_FILLING_MODE` flags: FOK=1, IOC=2, BOC=4.
- `ORDER_FILLING_RETURN` always available **except** Market execution mode (`SYMBOL_TRADE_EXECUTION_MARKET`).
- **Pending orders** → use `ORDER_FILLING_RETURN` (not filled at send time).
- Prefer `CTrade::SetTypeFillingBySymbol(symbol)` ([docs](https://www.mql5.com/en/docs/standardlibrary/tradeclasses/ctrade/ctradesettypefillingbysymbol)).
- Bad fill → `TRADE_RETCODE_INVALID_FILL` (10030).

### Standard Library trading

```cpp
#include <Trade/Trade.mqh>
CTrade trade;
trade.SetExpertMagicNumber(MAGIC);
trade.SetDeviationInPoints(30);
trade.SetTypeFillingBySymbol(_Symbol);
trade.Buy(lots, _Symbol, 0, sl, tp, "comment");
uint rc = trade.ResultRetcode(); // TRUST THIS
```

### Common trade retcodes

Full list: [Trade Server Return Codes](https://www.mql5.com/en/docs/constants/errorswarnings/enum_trade_return_codes)

| Code | Constant | Meaning |
|------|----------|---------|
| 10008 | `PLACED` | Order placed |
| 10009 | `DONE` | Done |
| 10010 | `DONE_PARTIAL` | Partial |
| 10016 | `INVALID_STOPS` | Bad SL/TP |
| 10019 | `NO_MONEY` | Margin |
| 10030 | `INVALID_FILL` | Bad filling |
| 10027 | `CLIENT_DISABLES_AT` | AutoTrading off in terminal |
| 10045 | `FIFO_CLOSE` | FIFO close rule |

### Position properties

Docs: [Position Properties](https://www.mql5.com/en/docs/constants/tradingconstants/positionproperties)

| Property | Note |
|----------|------|
| `POSITION_TICKET` | Use in `MqlTradeRequest::position`; may change after server service ops |
| `POSITION_IDENTIFIER` | Stable life-cycle id; links orders/deals; **unchanged** on netting reverse (ticket changes) |
| `POSITION_TYPE` | `BUY` / `SELL` |
| `VOLUME`, `PRICE_OPEN`, `SL`, `TP`, `PRICE_CURRENT`, `SWAP`, `PROFIT` | Doubles |
| `MAGIC`, `SYMBOL`, `COMMENT`, `REASON`, `EXTERNAL_ID` | Filter / audit |

Netting: one position per symbol. Hedging: many; always pass ticket on close/modify.

### Deal properties

Docs: [Deal Properties](https://www.mql5.com/en/docs/constants/tradingconstants/dealproperties)

After `HistorySelect` / `HistoryDealGetTicket`:

| Focus | Identifiers |
|-------|-------------|
| Identity | `DEAL_TICKET`, `DEAL_ORDER`, `DEAL_POSITION_ID`, `DEAL_MAGIC` |
| Economics | `VOLUME`, `PRICE`, `COMMISSION`, `SWAP`, `PROFIT`, `FEE`, `SL`, `TP` |
| Classification | `DEAL_TYPE` (BUY/SELL + balance/credit/bonus/…), `DEAL_ENTRY` |
| Audit | `REASON` (CLIENT/EXPERT/SL/TP/SO/ROLLOVER/…), `EXTERNAL_ID` |

**`DEAL_ENTRY`:** `IN` open/add · `OUT` close · `INOUT` reverse · `OUT_BY` close-by.

PnL stats: usually only BUY/SELL with `ENTRY != IN` (fixs that fix P/L). Ignore balance/credit types in strategy metrics.

### Practical trade gotchas

- Netting vs Hedging: `AccountInfoInteger(ACCOUNT_MARGIN_MODE)`.
- Validate stops vs `SYMBOL_TRADE_STOPS_LEVEL` / freeze level / tick size.
- BUY exit/TP distance uses **Bid**; SELL uses **Ask**.
- When closing by index, iterate high → low.
- Some Market-execution symbols need open without SL/TP then modify.

### MqlTradeRequest fields (official)

Docs: [MqlTradeRequest](https://www.mql5.com/en/docs/constants/structures/mqltraderequest)

| Field | Role |
|-------|------|
| `action` | `TRADE_ACTION_DEAL/PENDING/SLTP/MODIFY/REMOVE/CLOSE_BY` |
| `magic` | EA id for analytics / filtering |
| `order` | Pending ticket (modify/delete) |
| `symbol` | Symbol (not always needed for close/modify) |
| `volume` | Lots requested |
| `price` | Open / pending price; **Market execution** deals may omit |
| `stoplimit` | StopLimit child Limit price |
| `sl` / `tp` | Stop levels |
| `deviation` | Max slippage in points |
| `type` | `ORDER_TYPE_*` |
| `type_filling` | FOK / IOC / BOC / RETURN |
| `type_time` / `expiration` | GTC / DAY / SPECIFIED |
| `comment` | Order comment |
| `position` | **Required** on hedge close/modify |
| `position_by` | Opposite ticket for close-by |

**Required fields by execution mode (open DEAL):**

| Mode | Must set |
|------|----------|
| Request / Instant | action, symbol, volume, price, sl, tp, deviation, type, type_filling |
| Market / Exchange | action, symbol, volume, type, type_filling (+ magic/comment optional) |

Always zero-init: `MqlTradeRequest request={}; MqlTradeResult result={};`

### Order types · states · filling matrix

Docs: [Order Properties](https://www.mql5.com/en/docs/constants/tradingconstants/orderproperties)

**Types:** BUY/SELL, BUY/SELL_LIMIT, BUY/SELL_STOP, BUY/SELL_STOP_LIMIT, CLOSE_BY.

**States:** STARTED → PLACED → PARTIAL/FILLED | CANCELED | REJECTED | EXPIRED (+ REQUEST_* while in flight).

**Filling vs execution (official table):**

| | FOK | IOC | RETURN |
|--|-----|-----|--------|
| Instant / Request | always | always | always |
| Market | per symbol flags | per symbol flags | **disabled** |
| Exchange | per symbol flags | per symbol flags | always |

Pending → always prefer `ORDER_FILLING_RETURN`. BOC only for Limit / StopLimit (passive book).

**Order lifetime:** `ORDER_TIME_GTC`, `DAY`, `SPECIFIED`, `SPECIFIED_DAY` (expires 23:59:59 of day).

### OnTrade / OnTradeTransaction

Docs: [OnTrade](https://www.mql5.com/en/docs/event_handlers/ontrade) · [OnTradeTransaction](https://www.mql5.com/en/docs/event_handlers/ontradetransaction)

- EA only. Call `HistorySelect` before reading history deals/orders.
- One request can produce **many** trade events; do not assume 1:1.
- `OnTrade()` runs **after** related `OnTradeTransaction()` calls; counts may differ.
- Prefer `OnTradeTransaction` for precise deal/order/position deltas; use `OnTrade` for aggregate refresh.

`void OnTradeTransaction(const MqlTradeTransaction& trans, const MqlTradeRequest& request, const MqlTradeResult& result);`

| `trans.type` | Meaning |
|--------------|---------|
| `ORDER_ADD/UPDATE/DELETE` | Active order list change |
| `DEAL_ADD/UPDATE/DELETE` | History deal change |
| `HISTORY_ADD/UPDATE/DELETE` | History order change |
| `POSITION` | Position change **not** from a deal (e.g. SL/TP modify) |
| `REQUEST` | Request processed — **then** read `request` + `result` |

Official notes:

- Buy market → chain: request → order add → fill → order delete → history add → deal add → position. Arrival **order not guaranteed**.
- While handling one tx, account state may already change; queue length **1024** — slow handlers drop old events.
- For `DEAL_ADD`, select deal then read `DEAL_EXTERNAL_ID` if exchange ticket needed.

### MqlTradeTransaction field fill

Docs: [MqlTradeTransaction](https://www.mql5.com/en/docs/constants/structures/mqltradetransaction)

| `trans.type` | Meaningful fields |
|--------------|-------------------|
| `ORDER_*` / `HISTORY_*` | order, symbol, order_type/state, time_*, prices, volume, position(/_by) |
| `DEAL_*` | deal, order, symbol, deal_type, price, sl/tp, volume, position(/_by) |
| `POSITION` | symbol, deal_type as side, avg price, sl/tp, volume — **not** fired when deal itself opens/closes pos |
| `REQUEST` | **only** `type`; use `request` + `result` args |

### OrderCheck

Docs: [OrderCheck](https://www.mql5.com/en/docs/trading/ordercheck)

```cpp
MqlTradeCheckResult check={};
if(!OrderCheck(request, check)) { /* GetLastError + check.retcode/comment */ }
// success → check.balance/equity/margin/margin_free/margin_level
```

True = structure OK / basic check passed — **not** a guarantee `OrderSend` succeeds (e.g. market closed → send fails after check OK).

---

## 5. Market / Account / Terminal

- [Market Info](https://www.mql5.com/en/docs/marketinfo): `SymbolSelect`, `SymbolInfoInteger/Double/String`, `SymbolInfoTick`, depth of market.
- [Account](https://www.mql5.com/en/docs/account): `AccountInfoDouble/Integer/String` — balance, equity, margin, currency, trade mode, FIFO (`ACCOUNT_MARGIN_MODE`, `ACCOUNT_FIFO_CLOSE`).
- [Common / Terminal](https://www.mql5.com/en/docs/common): `TerminalInfoInteger`, DLL/WebRequest allow flags, community connection.
- Always `SymbolSelect(_Symbol, true)` before heavy symbol queries in multi-symbol EAs.

### Date & time

Docs: [Date and Time](https://www.mql5.com/en/docs/dateandtime)

| Call | Meaning |
|------|---------|
| `TimeCurrent` | Last quote time (server) |
| `TimeTradeServer` | Trade server calculation clock (calendar APIs use this) |
| `TimeLocal` / `TimeGMT` | PC local / GMT (+ DST helpers) |
| `TimeToStruct` / `StructToTime` | ↔ `MqlDateTime` |

High-res timers: `GetTickCount` / `GetMicrosecondCount`, not `datetime`.

### Terminal global variables

Docs: [Globals](https://www.mql5.com/en/docs/globals) — **not** the same as C++ file-scope globals.

- Shared across all MQL5 programs in the terminal.
- Persist **4 weeks** after last access (read or write); then auto-deleted.
- `GlobalVariableSetOnCondition` for simple locks; `GlobalVariableTemp` = session-only; `Flush` to disk.

### OnDeinit reasons

Docs: [OnDeinit](https://www.mql5.com/en/docs/event_handlers/ondeinit)

| Code | When |
|------|------|
| `REASON_PROGRAM` (0) | `ExpertRemove()` |
| `REASON_REMOVE` (1) | Removed from chart |
| `REASON_RECOMPILE` (2) | Recompiled |
| `REASON_CHARTCHANGE` (3) | Symbol/TF change |
| `REASON_CHARTCLOSE` (4) | Chart closed |
| `REASON_PARAMETERS` (5) | Inputs changed |
| `REASON_ACCOUNT` (6) | Account switch / reconnect settings |
| `REASON_TEMPLATE` (7) | Template applied |
| `REASON_INITFAILED` (8) | `OnInit` non-zero |
| `REASON_CLOSE` (9) | Terminal exit |

Also: `_UninitReason` / `UninitializeReason()`. On chartchange/params, Init follows Deinit — release handles cleanly then recreate.

---

## 6. Timeseries & Indicators Access

Docs: [Timeseries](https://www.mql5.com/en/docs/series)

- Timeseries indexing: index `0` = **current (newest)** bar when series mode.
- Prefer `Copy*` into arrays; physical storage grows at end — series flag only changes indexing.
- **Indicators**: `Copy*` fails immediately if data not ready (async).
- **EAs/Scripts**: runtime retries briefly to allow download/calc.
- Hot path (`OnTick`/`OnCalculate`): prefer **static/preallocated** arrays over repeated alloc.
- Release unused indicator handles: `IndicatorRelease`.
- Built-in indicators via `iMA`/`iATR`/… return handles → `CopyBuffer`.

| API | Role |
|-----|------|
| `CopyRates` / `CopySeries` | Bar structs / synced fields |
| `CopyOpen/High/Low/Close/Time/…` | Single-field series |
| `CopyBuffer` | Indicator buffer by handle+index |
| `CopyTicks` / `CopyTicksRange` | Tick stream |
| `Bars` / `BarsCalculated` | History depth / indicator ready count |
| `iBarShift` / `iHighest` / `iLowest` | Classic shift helpers |
| `IndicatorCreate` / `Parameters` / `Release` | Generic handle lifecycle |

### MqlRates · MqlTick

Docs: [MqlRates](https://www.mql5.com/en/docs/constants/structures/mqlrates) · [MqlTick](https://www.mql5.com/en/docs/constants/structures/mqltick)

```cpp
struct MqlRates {
  datetime time; double open, high, low, close;
  long tick_volume; int spread; long real_volume;
};
struct MqlTick {
  datetime time; double bid, ask, last;
  ulong volume; long time_msc; uint flags; double volume_real;
};
```

- `CopyRates` → `MqlRates[]`; `SymbolInfoTick` / `CopyTicks` → `MqlTick`.
- Tick fields always filled (carry prior Ask/Bid/… even if only one changed). Inspect `flags`: `TICK_FLAG_BID/ASK/LAST/VOLUME/BUY/SELL`.

### iCustom path rules

Docs: [iCustom](https://www.mql5.com/en/docs/indicators/icustom)

- `"\\Name"` → search `MQL5\Name.ex5` from root.
- Else: (1) same folder as caller EX5, then (2) `MQL5\Indicators\…`.
- Subdirs: use `"MyFolder\\IndName"` (double backslash).
- Applied price / chained handle = **last** argument after custom `input`s.
- Non-constant name / `IndicatorCreate` → add `#property tester_indicator "file.ex5"` for tester.
- Failure → `INVALID_HANDLE`, error **4802**.

---

## 7. Custom Indicators

Docs: [Custom Indicators](https://www.mql5.com/en/docs/customind) · `OnCalculate`

- Set `#property indicator_buffers` / `indicator_plots` and matching `SetIndexBuffer`.
- Buffer types: `INDICATOR_DATA`, `INDICATOR_COLOR_INDEX`, `INDICATOR_CALCULATIONS`.
- Small-range plots: set `INDICATOR_MINIMUM` / `MAXIMUM` or values may look blank.
- `PLOT_DRAW_BEGIN`, `PLOT_EMPTY_VALUE=EMPTY_VALUE`.
- Recalc from `prev_calculated` (usually `prev_calculated-1` for current bar updates).
- Return `rates_total` on success.
- Indicators **must not** call trade functions / `WebRequest`.

### Draw styles

Docs: [Drawing Styles](https://www.mql5.com/en/docs/constants/indicatorconstants/drawstyles)

| Style | Data bufs | Color buf |
|-------|-----------|-----------|
| `DRAW_NONE/LINE/SECTION/HISTOGRAM/ARROW` | 1 | 0 |
| `DRAW_HISTOGRAM2/ZIGZAG/FILLING` | 2 | 0 |
| `DRAW_BARS/CANDLES` | 4 | 0 |
| `DRAW_COLOR_*` variants | same + | 1 |

Color buffers bind **after** value buffers. Plot props: `PLOT_DRAW_BEGIN`, `EMPTY_VALUE`, `LINE_STYLE/WIDTH/COLOR`, `LABEL`, `SHIFT`, `ARROW`. Line styles (`STYLE_SOLID`…) only for width 0–1.

Buffer roles (`SetIndexBuffer`): `INDICATOR_DATA` · `INDICATOR_COLOR_INDEX` · `INDICATOR_CALCULATIONS` (aux, not plotted).

Indicator window props ([docs](https://www.mql5.com/en/docs/constants/indicatorconstants/customindicatorproperties)): `INDICATOR_DIGITS`, `MINIMUM`/`MAXIMUM`, `LEVELS` + `LEVELVALUE/COLOR/STYLE`, `SHORTNAME`, fixed height flags.

### Minimal pattern

```cpp
#property indicator_chart_window
#property indicator_buffers 1
#property indicator_plots   1
double Buf[];
int OnInit() {
  SetIndexBuffer(0, Buf, INDICATOR_DATA);
  ArraySetAsSeries(Buf, true);
  return INIT_SUCCEEDED;
}
int OnCalculate(const int rates_total, const int prev_calculated,
                const datetime &time[], const double &open[],
                const double &high[], const double &low[],
                const double &close[], const long &tick_volume[],
                const long &volume[], const int &spread[]) {
  if(rates_total < 2) return 0;
  int start = (prev_calculated > 1) ? prev_calculated - 1 : 0;
  for(int i = start; i < rates_total && !IsStopped(); i++)
    Buf[i] = close[i]; // respect ArraySetAsSeries on close[] if set
  return rates_total;
}
```

### 7b. OnCalculate deep dive

Docs: [OnCalculate](https://www.mql5.com/en/docs/event_handlers/oncalculate)

**Only one of the two overloads** may exist in a single indicator.

| Form | Signature idea | When |
|------|----------------|------|
| Applied-price | `(rates_total, prev_calculated, begin, price[])` | User picks Apply-to; `_AppliedTo` |
| OHLC series | `(rates_total, prev_calculated, time, open, high, low, close, tick_volume, volume, spread)` | Full bar data |

Rules (official):

- Return value becomes next call’s `prev_calculated`. **Return 0** → nothing shown in DataWindow.
- History load / gap fill → terminal sets `prev_calculated = 0` (full recalc).
- Call `ArraySetAsSeries` yourself — do not rely on defaults for `time[]`/`open[]`/…
- `begin` skips leading invalid data in applied-price form; start calc at `(period-1)+begin`.
- Other programs read via `iCustom()`; applied-price/`handle` is the **last** custom input when chaining.

Incremental loop pattern (series=false indexing as in docs samples):

```cpp
int pos = prev_calculated - 1;
if(pos < start) pos = start;
for(int i = pos; i < rates_total && !IsStopped(); i++)
  Buf[i] = /* calc */;
return rates_total;
```

---

## 8. Charts, Objects, UI

Docs: [Objects](https://www.mql5.com/en/docs/objects) · [Chart operations](https://www.mql5.com/en/docs/chart_operations)

**Chart ops** (`ChartSetInteger/Double/String`) are asynchronous — queued; force with `ChartRedraw()`. Useful: `ChartID`, `ChartTimePriceToXY` / `XYToTimePrice` (panel hit-testing), `ChartNavigate`, `ChartIndicatorAdd/Delete/Get`, `ChartScreenShot`, `ChartOpen/Close/Next`, drop coords `Chart*OnDropped`.

- Object create/set/move enqueue chart commands — **not** instantly visible; call `ChartRedraw()` after UI batches.
- Names must be unique per chart (incl. subwindows). Rename = DELETE old + CREATE new events.
- Core API: `ObjectCreate`, `ObjectDelete`, `ObjectsDeleteAll`, `ObjectFind`, `ObjectMove`, `ObjectsTotal`, `ObjectGet*/Set*`, `ObjectGetValueByTime` / `GetTimeByValue`.
- Text resources: `TextSetFont`, `TextOut`, `TextGetSize`.
- Standard UI: [`Include\Controls\`](https://www.mql5.com/en/docs/standardlibrary/controls) (dialogs, buttons, edits).
- Delete objects by iterating **reverse** order.
- Separate name prefixes for panel widgets vs chart trading lines.

**Panel-relevant `ENUM_OBJECT`:** `OBJ_RECTANGLE_LABEL`, `OBJ_LABEL`, `OBJ_BUTTON`, `OBJ_EDIT`, `OBJ_BITMAP_LABEL`, `OBJ_HLINE`/`TREND` (levels), `OBJ_EVENT` (calendar tag). Full list: [Object Types](https://www.mql5.com/en/docs/constants/objectconstants/enum_object).

### 8b. Chart events deep dive

Docs: [ENUM_CHART_EVENT](https://www.mql5.com/en/docs/constants/chartconstants/enum_chartevents)

| Event | Enable flag (if needed) | lparam | dparam | sparam |
|-------|-------------------------|--------|--------|--------|
| `KEYDOWN` / `KEYUP` | — | key code | repeats (keyup=1) | modifier bitmask string |
| `MOUSE_MOVE` | `CHART_EVENT_MOUSE_MOVE` | X | Y | button bitmask string |
| `MOUSE_WHEEL` | `CHART_EVENT_MOUSE_WHEEL` | packed flags+XY | delta (±120) | — |
| `CLICK` | — | X | Y | — |
| `OBJECT_CLICK` | — | X | Y | object name |
| `OBJECT_DRAG` / `ENDEDIT` / `CHANGE` | — | — | — | object name |
| `OBJECT_CREATE` / `DELETE` | `CHART_EVENT_OBJECT_CREATE/DELETE` | — | — | object name |
| `CHART_CHANGE` | — | — | — | — |
| `CUSTOM` … `CUSTOM_LAST` | via `EventChartCustom` | custom | custom | custom |

Panel checklist:

```cpp
ChartSetInteger(0, CHART_EVENT_MOUSE_MOVE, true);
// optional: CHART_CONTEXT_MENU=false, CHART_CROSSHAIR_TOOL=false for drag UX
ChartRedraw(); // after enabling event props
```

`MOUSE_MOVE` sparam bits: 1 L, 2 R, 4 SHIFT, 8 CTRL, 16 Mid, 32/64 X buttons.  
`TranslateKey(key)` for KEYDOWN/KEYUP text (incl. dead keys).

### 8c. Economic Calendar

Docs: [Calendar](https://www.mql5.com/en/docs/calendar) · [structures](https://www.mql5.com/en/docs/constants/structures/mqlcalendar)

| API | Use |
|-----|-----|
| `CalendarCountries` / `CalendarCountryById` | ISO country list |
| `CalendarEventByCountry` / `ByCurrency` / `ById` | event meta |
| `CalendarValueHistory` / `HistoryByEvent` | range query (**TimeTradeServer** timezone) |
| `CalendarValueLast` / `LastByEvent` | incremental via `change_id` |

Structures: `MqlCalendarCountry`, `MqlCalendarEvent`, `MqlCalendarValue`.

**Critical:** `actual_value` / `prev_value` / `forecast_value` / `revised_prev_value` are stored ×1e6, or `LONG_MIN` if unset. Prefer:

```cpp
if(v.HasActualValue()) double x = v.GetActualValue(); // else nan
```

Importance: `CALENDAR_IMPORTANCE_LOW/MODERATE/HIGH`. Filter by currency matching symbol base/quote for news filters / COT-adjacent workflows.

---

## 9. Files, Strings, Math, DateTime

Docs: [File Functions](https://www.mql5.com/en/docs/files)

Sandbox only:

- `TERMINAL_DATA_PATH\MQL5\Files\`
- Common: `TERMINAL_COMMONDATA_PATH\Files\` via `FILE_COMMON`

| Flag group | Examples |
|------------|----------|
| Access | `FILE_READ`, `FILE_WRITE`, `FILE_REWRITE` |
| Format | `FILE_BIN`, `FILE_TXT`, `FILE_CSV` |
| Encoding | `FILE_ANSI`, `FILE_UNICODE` |
| Share | `FILE_SHARE_READ`, `FILE_SHARE_WRITE` |
| Place | `FILE_COMMON`, `FILE_TXT`+CSV delimiter in open |

`FileOpen` creates missing subfolders on write. Named pipes supported via `FileOpen` params.  
Also: `FileLoad`/`FileSave`, `FolderCreate`/`Delete`/`Clean`, find APIs.

- Strings: prefer `StringFormat`; beware encoding when mixing File + Web.
- Time: `TimeCurrent` (last tick server), `TimeTradeServer`, `TimeLocal`, `TimeGMT`. Calendar APIs use **trade server** time.

### Conversion

Docs: [Convert](https://www.mql5.com/en/docs/convert)

- **`NormalizeDouble`**: required accuracy for prices sent to server — never send unnormalized prices with extra digits.
- Still align to `SYMBOL_TRADE_TICK_SIZE` before normalize (see Quick Reference).
- `EnumToString`, `TimeToString`/`StringToTime`, `DoubleToString`, `ColorToARGB`, char/short array ↔ string for network/files.

### Strings

Docs: [Strings](https://www.mql5.com/en/docs/strings)

Hot path: `StringFormat` / `StringConcatenate`, `StringFind`/`Replace`/`Split`/`Substr`, `TrimLeft/Right`, `ToLower/Upper`, `StringReserve` before heavy builds. Prefer reserve/concat over repeated `+` in loops.

### Math

Docs: [Math](https://www.mql5.com/en/docs/math)

Standard trig/exp/log/round + `MathIsValidNumber`, `MathCompareByDigits`, `MathSrand`/`Rand`. Many ops also work **element-wise** on matrices/vectors.

---

## 10. Network / WebRequest

Docs: [WebRequest](https://www.mql5.com/en/docs/network/webrequest)

Two overloads: form-urlencoded (cookie/referer) · custom headers (`"Key: Value\r\n"`).

- Returns HTTP code, or **-1** on error (`GetLastError`).
- **EA + Script only** — indicator → **4014** (shared symbol thread; must not block).
- URL allow-list: Tools → Options → Expert Advisors (port from `http`/`https`).
- Synchronous (blocks). **Not** available in Strategy Tester.
- Also: sockets / TCP + TLS under Network section.

---

## 11. SQLite / Matrix / OpenCL / Python

### SQLite

Docs: [Database](https://www.mql5.com/en/docs/database)

Whole DB = one file (or `DATABASE_OPEN_MEMORY` for RAM, app-private).

| Flow | Calls |
|------|-------|
| Open/close | `DatabaseOpen` / `Close` |
| DDL/DML | `DatabaseExecute` |
| Prepared | `Prepare` → `Bind`/`BindArray` → `Read`/`ReadBind` → `Finalize`/`Reset` |
| Bulk speed | wrap in `TransactionBegin` / `Commit` / `Rollback` (100×+ vs auto-commit) |
| I/O | `Import` / `Export` / `Print` / `TableExists` |

Use `DatabaseReadBind` to fill a struct in one shot. SQL includes stats (`median`, `percentile_*`, `stddev`, `mode`) and math (`sin`, `pow`, …). Prefer SQL over hand-rolled loops for large history dumps / optimization logs.

### Matrix · OpenCL · Python

- [Matrix & Vector](https://www.mql5.com/en/docs/matrix): concise numeric code; many `Math*` ops are element-wise.
- [OpenCL](https://www.mql5.com/en/docs/opencl): GPU acceleration.
- [Python](https://www.mql5.com/en/docs/python_metatrader5): drive terminal via `MetaTrader5` package for research/automation.

---

## 12. Standard Library

Docs: [Standard Library](https://www.mql5.com/en/docs/standardlibrary) under `MQL5/Include`

| Area | Path |
|------|------|
| Trade | `Include\Trade\` (`CTrade`, `CPositionInfo`, `CSymbolInfo`…) |
| Expert framework | `Include\Expert\` |
| Controls / panels | `Include\Controls\` |
| Arrays / Generic | `Include\Arrays\`, `Include\Generic\` |
| Canvas / Graphics | `Include\Canvas\`, `Include\Graphics\` |
| Indicators wrappers | `Include\Indicators\` |
| Files / Strings | `Include\Files\`, `Include\Strings\` |
| Math / OpenCL | `Include\Math\`, `Include\OpenCL\` |

### Trade classes (`Include\Trade\`)

Docs: [Trade Classes](https://www.mql5.com/en/docs/standardlibrary/tradeclasses)

| Class | Role |
|-------|------|
| `CTrade` | Send deals / pending / modify / close |
| `CPositionInfo` | Read open positions |
| `COrderInfo` / `CHistoryOrderInfo` | Pending + history orders |
| `CDealInfo` | History deals |
| `CSymbolInfo` | Symbol props cache |
| `CAccountInfo` | Account props |
| `CTerminalInfo` | Terminal environment |

Typical init (from official OnTester sample):

```cpp
trade.SetExpertMagicNumber(MAGIC);
trade.SetMarginMode();
trade.SetTypeFillingBySymbol(_Symbol);
trade.SetDeviationInPoints(slippage);
```

Prefer StdLib wrappers for production speed unless you need raw `OrderSend` control.

---

## 13. Testing & Optimization

Docs: [OnTester](https://www.mql5.com/en/docs/event_handlers/ontester) · Testing section of Reference

- Strategy Tester: no `WebRequest`; trade/network limitations apply.
- `double OnTester()` → custom optimization criterion (**higher = better**; genetic sort descending).
- Also: `OnTesterInit` / `OnTesterDeinit` / `OnTesterPass` for frames / multi-pass data.
- Thin sample history (< ~10 trades) → often return `0` so genetics discard junk.
- Single-test vs optimize: check `MQLInfoInteger(MQL_TESTER)` and `MQL_OPTIMIZATION`.

---

## 14. Panel EA Patterns

(Practical patterns on top of Controls + Chart events.)

### Architecture

```
Constants.mqh → Types.mqh → Helpers.mqh → Panel.mqh
Engine.mqh ← Orders.mqh ← Drawing.mqh
```

### Edit safety

Never `ObjectSetString(..., OBJPROP_TEXT)` on the field currently being edited (`CHARTEVENT_OBJECT_BEGINEDIT` / `ENDEDIT`).

### Buttons

On `CHARTEVENT_OBJECT_CLICK` for `OBJ_BUTTON`, handle once and set `OBJPROP_STATE` back to `false`.

### Drag

- Panel drag: shift all objects with shared prefix by delta (don’t rebuild every pixel).
- Chart HLINE/TREND: update from `CHARTEVENT_OBJECT_DRAG`; finalize on change/end; normalize price to tick size.

### Trade helpers

```cpp
#include <Trade/Trade.mqh>
CTrade g_trade;
void TM_OrdersInit() {
  g_trade.SetExpertMagicNumber(TP_MAGIC);
  g_trade.SetDeviationInPoints(30);
  g_trade.SetTypeFillingBySymbol(_Symbol);
}
```

### Multi-TP local exits

BUY: measure with **Bid**; SELL: with **Ask**. Partial close via `PositionClosePartial`.

### Common panel mistakes

| Mistake | Fix |
|---------|-----|
| Trading from indicator | Move logic to EA |
| Hardcoded filling | `SetTypeFillingBySymbol` |
| Same prefix for UI + lines | Split prefixes |
| Forward delete loops | Reverse iterate |
| Hex colors | Use `C'R,G,B'` |

---

## 15. Compilation

- MetaEditor: open `.mq5` → **F7** (Compile).
- Success = `.ex5` updated + log shows 0 errors.
- Includes resolved from `/Include` and local `"quotes"`.
- Wine/macOS: map MQL5 to `X:` and call `MetaEditor64.exe /compile:...` (exit code may be 1 even on success — verify `.ex5`).

---

## 16. Docs Research Workflow

1. Classify: language / event / trade / series / object / file / network / stdlib.
2. Open the atlas link for that class.
3. Confirm signature + required enums/flags.
4. Answer with: cause → official URL → code fix → what to log.
5. Never invent undocumented overloads.

Primary: https://www.mql5.com/en/docs  
Chinese: https://www.mql5.com/zh/docs

---

## 17. Runtime errors (frequent)

Docs: [Error Codes](https://www.mql5.com/en/docs/constants/errorswarnings/errorcodes) — `GetLastError` / `_LastError`; clear with `ResetLastError`.

| Code | Constant | Typical cause |
|------|----------|---------------|
| 4014 | `FUNCTION_NOT_ALLOWED` | Trade/`WebRequest`/`Sleep` from wrong program type |
| 4024 | `INVALID_HANDLE` | Bad indicator/file/socket handle |
| 4108 | `CHART_CANNOT_CREATE_TIMER` | `EventSetTimer` failed |
| 4202 | `OBJECT_NOT_FOUND` | Wrong object name |
| 4301/4302 | market unknown / not selected | Call `SymbolSelect` first |
| 4401–4407 | history errors | Bars not ready / timeout / buffer small |
| 4752–4758 | trade context | AutoTrading / trade disabled |
| 4806–4802 | indicator buffer/copy | Wrong buffer / data not ready |

Trade **server** retcodes (10004+) are separate — see §4; do not confuse with runtime 4xxx.

---

## 18. Quick Reference

```cpp
// Pip
int digits = (int)SymbolInfoInteger(_Symbol, SYMBOL_DIGITS);
double pip = (digits == 3 || digits == 5) ? _Point * 10.0 : _Point;

// Normalize to tick
double NormalizePrice(double price) {
  double tick = SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_SIZE);
  if(tick > 0) price = MathRound(price / tick) * tick;
  return NormalizeDouble(price, (int)SymbolInfoInteger(_Symbol, SYMBOL_DIGITS));
}

// CTrade init
#include <Trade/Trade.mqh>
CTrade trade;
trade.SetExpertMagicNumber(MAGIC);
trade.SetDeviationInPoints(30);
trade.SetTypeFillingBySymbol(_Symbol);

// Position loop
for(int i = PositionsTotal() - 1; i >= 0; i--) {
  ulong ticket = PositionGetTicket(i);
  if(ticket == 0) continue;
  if(PositionGetString(POSITION_SYMBOL) != _Symbol) continue;
  if(PositionGetInteger(POSITION_MAGIC) != MAGIC) continue;
}

// Object reverse delete
for(int i = ObjectsTotal(0) - 1; i >= 0; i--) {
  string name = ObjectName(0, i);
  if(StringFind(name, prefix) == 0)
    ObjectDelete(0, name);
}
```

| Need | Call |
|------|------|
| Ask/Bid | `SymbolInfoDouble(_, SYMBOL_ASK/BID)` |
| Tick size | `SYMBOL_TRADE_TICK_SIZE` |
| Filling flags | `SYMBOL_FILLING_MODE` |
| Execution mode | `SYMBOL_TRADE_EXEMODE` |
| Margin mode | `ACCOUNT_MARGIN_MODE` |
| Last trade result | `trade.ResultRetcode()` |

---

## Exness note

When developing for Exness MT5, still follow the same official MQL5 rules; validate symbol filling/execution/stops on the live symbol list. Intro link used in this org’s projects: https://one.toexness.com/intl/zh/a/30852
