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
| 8 | [Charts, Objects, UI](#8-charts-objects-ui) | [/en/docs/chart_operations](https://www.mql5.com/en/docs/chart_operations) |
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

### Practical trade gotchas

- Netting vs Hedging: `AccountInfoInteger(ACCOUNT_MARGIN_MODE)`.
- Validate stops vs `SYMBOL_TRADE_STOPS_LEVEL` / freeze level / tick size.
- BUY exit/TP distance uses **Bid**; SELL uses **Ask**.
- When closing by index, iterate high → low.
- Some Market-execution symbols need open without SL/TP then modify.

---

## 5. Market / Account / Terminal

- [Market Info](https://www.mql5.com/en/docs/marketinfo): `SymbolSelect`, `SymbolInfoInteger/Double/String`, `SymbolInfoTick`, depth of market.
- [Account](https://www.mql5.com/en/docs/account): balance, equity, margin, currency, trade mode, FIFO.
- [Common / Terminal](https://www.mql5.com/en/docs/common): `TerminalInfoInteger`, DLL/WebRequest allow flags, community connection.
- Always `SymbolSelect(_Symbol, true)` before heavy symbol queries in multi-symbol EAs.

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

---

## 8. Charts, Objects, UI

- [Chart operations](https://www.mql5.com/en/docs/chart_operations): `ChartRedraw`, `ChartID`, navigate, apply template.
- [Objects](https://www.mql5.com/en/docs/objects): `ObjectCreate` / `ObjectSetInteger` / `ObjectSetString` / `ObjectsTotal`.
- [Chart events](https://www.mql5.com/en/docs/constants/chartconstants/enum_chartevents): click, drag, keydown, custom.
- Standard UI: [`Include\Controls\`](https://www.mql5.com/en/docs/standardlibrary/controls) (dialogs, buttons, edits).
- Delete objects by iterating **reverse** order.
- Separate name prefixes for panel widgets vs chart trading lines.

---

## 9. Files, Strings, Math, DateTime

- Files live under `MQL5/Files` (or common folder with `FILE_COMMON`).
- Use correct flags: `FILE_READ/WRITE`, `FILE_BIN/TXT/CSV`, `FILE_UNICODE/ANSI`, `FILE_SHARE_*`.
- Strings: prefer `StringFormat`; beware encoding when mixing File + Web.
- Time: distinguish `TimeCurrent` (server), `TimeTradeServer`, `TimeLocal`, `TimeGMT`.

---

## 10. Network / WebRequest

Docs: [WebRequest](https://www.mql5.com/en/docs/network/webrequest)

- **EA + Script only** — calling from indicator → error **4014**.
- URL must be allow-listed in Tools → Options → Expert Advisors.
- Synchronous (blocks). Not available in Strategy Tester.
- Also: sockets / TCP + TLS APIs under Network section.

---

## 11. SQLite / Matrix / OpenCL / Python

- [Database](https://www.mql5.com/en/docs/database): SQLite built-ins for local storage/analytics.
- [Matrix & Vector](https://www.mql5.com/en/docs/matrix): concise numeric code.
- [OpenCL](https://www.mql5.com/en/docs/opencl): GPU acceleration.
- [Python](https://www.mql5.com/en/docs/python_metatrader5): drive terminal from Python (`MetaTrader5` package) for research/automation.

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

Prefer StdLib wrappers for production speed unless you need raw `OrderSend` control.

---

## 13. Testing & Optimization

- Strategy Tester supports indicators differently (many network/trade limitations).
- Use `OnTester`, `OnTesterInit`, `OnTesterDeinit`, `OnTesterPass` for custom metrics / frames.
- Optimization types documented under Testing section — pick criterion consciously (balance, custom `OnTester` return).

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

## 17. Quick Reference

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
