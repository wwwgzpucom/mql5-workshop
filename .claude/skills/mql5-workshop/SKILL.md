---
name: mql5-workshop
description: >-
  Routing-first MQL5 skill backed by official Reference + offline mql5.chm corpus.
  Use for ANY MQL5 coding, debugging, compiling, retcodes, indicators, panels, tester.
  Never invent APIs — look up signatures in linked docs/corpus pages. Prefer Standard
  Library Trade/Controls when suitable. Exness MT5 compatible.
---

# MQL5 Workshop

**Source of truth (in order):**
1. Official Reference — https://www.mql5.com/en/docs · https://www.mql5.com/zh/docs
2. Offline CHM corpus (when present): `mql5-ebook-corpus/pages/<file>.htm.txt`
3. This skill — routing, verified hard rules, anti-patterns, playbooks

**Never invent APIs, overloads, or retcodes.** If unsure → open the linked page / corpus file, then code.

**Corpus tip:** topic `OnCalculate` → `mql5-ebook-corpus/pages/oncalculate.htm.txt`

---

## 0. Task router (start here)

| User need | Jump | Lookup first |
|-----------|------|----------------|
| Open/close/modify position or pending | [Trade playbook](#3-trade-playbook) | `trading.htm`, `mqltraderequest.htm`, `orderproperties.htm` |
| Retcode / OrderSend failed | [Trade](#3-trade-playbook) + [Errors](#8-errors--retcodes) | `enum_trade_return_codes.htm`, `errorcodes.htm` |
| Custom indicator / buffer / plot | [Indicator playbook](#4-indicator-playbook) | `oncalculate.htm`, `drawstyles.htm` |
| iCustom / CopyBuffer / timeseries | [Series](#5-series--handles) | `series.htm`, `icustom.htm` |
| Chart UI / panel / objects / drag | [Panel playbook](#6-panel--ui-playbook) | `enum_chartevents.htm`, `objects.htm` |
| Economic calendar / news filter | [Calendar](#7-calendar-files-network) | calendar structures |
| WebRequest / sockets | [Network](#7-calendar-files-network) | `webrequest.htm` |
| Strategy Tester / optimize / frames | [Tester](#9-tester--optimization) | `testing.htm`, `ontester.htm` |
| Language / OOP / #property / inputs | [Language](#1-language--runtime-hard-rules) | `syntax.htm`, `inputvariables.htm` |
| Compile F7 | [Compile](#10-compile--ship-checklist) | MetaEditor |

**Debug answer format:** cause → official/corpus page → minimal fix → what to log (`retcode`, `GetLastError`, symbol props).

---

## 1. Language & runtime hard rules

(Verified against official Reference / CHM.)

### Not C++

- No address arithmetic, no `goto`, no anonymous enums, **no multiple inheritance**.
- Identifiers ≤ **63** chars.
- Structures packed **without padding** (like `#pragma pack(1)`).
- Object pointers are **8-byte descriptors**, not raw addresses. Pass objects by reference.
- `new` only for **classes** (not structs/arrays) → `delete` then NULL.
- `complex`: by **value** to MQL5 funcs; to DLL **by reference only**.
- Functions ≤ **64** params; cannot `return` arrays / class objects / compound structs.
- `switch` expr must be `int` / `uint`.
- Matrix multiply: operator `@` (matching element types).

### Fatal / hard stops

- Division by zero · array OOB · bad object pointer → **program terminates**.
- Long loops: check `IsStopped()`.
- Doubles: never raw `==`; use `NormalizeDouble(a-b, digits)==0`.
- Boolean ops **short-circuit**.

### Program types

| Type | Folder | Can trade | Notes |
|------|--------|-----------|-------|
| EA | `Experts` | Yes | Full events |
| Indicator | `Indicators` | **No** | No `WebRequest`, no `Sleep`, no trade |
| Script | `Scripts` | Yes | `OnStart` only |
| Service | `Services` | No chart bind | `OnStart` + custom chart events |
| Library | `Libraries` | — | `export`; no events |
| Include | `Include` | — | `#property` here is **ignored** |

Chart capacity: **1 EA + 1 script + unlimited indicators**.

Trade APIs: **EA + Script only**, and "Allow live trading" enabled.

### Init / events

- Prefer `int OnInit()`; `void OnInit()` is legacy (always success).
- `INIT_FAILED`: EA unloaded; indicator stays but **handlers die**.
- `INIT_PARAMETERS_INCORRECT`: skip opt set (red). `INIT_AGENT_NOT_SUITABLE`: ban agent for rest of optimization.
- Duplicate `NewTick` / `Timer` / `ChartEvent` while queued or handling → **not enqueued again**.
- `BookEvent`: never skipped; chart-wide → filter `symbol`; balance `MarketBookAdd`/`Release`.
- `OnTrade()` runs **after** related `OnTradeTransaction()`; counts not 1:1; arrival order **not guaranteed**.
- Transaction queue length **1024** — slow handlers drop old txs.
- One timer per program; live floor ~**10–16 ms**; kill in `OnDeinit`.

### Inputs

- `input` read-only in code; re-init before every `OnInit`.
- `sinput`: visible, **not** optimized (unless `ParameterSetRange` in `OnTesterInit`).
- `input group "Name"` for UI blocks. Scripts need `#property script_show_inputs`.
- No arrays / complex types as inputs.

Lookup: `syntax.htm`, `classes.htm`, `object_pointers.htm`, `oninit.htm`, `event_handlers.htm`, `inputvariables.htm`, `runtime.htm`

---

## 2. Anti-patterns (fix these first)

| Wrong | Right | Page |
|-------|-------|------|
| Trade / `WebRequest` / `Sleep` in indicator | Move to EA/script; `Sleep` **forbidden** in indicators | `webrequest.htm`, `sleep.htm` |
| Hardcoded `ORDER_FILLING_FOK` | `SetTypeFillingBySymbol` / `SYMBOL_FILLING_MODE` | `orderproperties.htm` |
| `ORDER_FILLING_RETURN` on Market-execution symbol | RETURN **disabled** in Market mode | `orderproperties.htm` |
| Pending with FOK/IOC | Pending → prefer **RETURN** | `orderproperties.htm` |
| Ignore retcode if `OrderSend` returns true | Always read `MqlTradeResult.retcode` | `mqltraderesult.htm` |
| Assume `OrderCheck` true ⇒ send succeeds | Check ≠ guaranteed execution | `ordercheck.htm` |
| `HistoryDealSelect` then expect full history list | Select **resets** cache — re-`HistorySelect` | `historyselect.htm` |
| Hedge close without `position` ticket | Set `MqlTradeRequest.position` | `mqltraderequest.htm` |
| Raw double compare for prices | Tick-align + `NormalizeDouble` | `relation.htm` |
| `#property` only in `.mqh` | Put `#property` in main `.mq5` | `compilation.htm` |
| Forward delete/close loops | Iterate **high → low** | objects / positions |
| `ObjectSet*` without redraw | `ChartRedraw()` | `objects.htm` |
| Calendar raw `actual_value` as display number | ÷1e6 or `GetActualValue()`; `LONG_MIN` unset | calendar structures |
| File write during Cloud opt | Frames; guard `MQL_OPTIMIZATION || MQL_FORWARD` | `testing.htm` |
| Set `OBJPROP_TEXT` while Edit focused | Wait `CHARTEVENT_OBJECT_ENDEDIT` | chart events |

---

## 3. Trade playbook

Docs: https://www.mql5.com/en/docs/trading · `trading.htm`, `mqltraderequest.htm`, `orderproperties.htm`

### Model

Order → Deal(s) → Position. Buy at **Ask**, Sell at **Bid**. One order may yield many deals.

### Send path

```cpp
#include <Trade/Trade.mqh>
CTrade trade;
trade.SetExpertMagicNumber(MAGIC);
trade.SetDeviationInPoints(30);
trade.SetMarginMode();
trade.SetTypeFillingBySymbol(_Symbol);

MqlTradeRequest req={}; MqlTradeResult res={}; MqlTradeCheckResult chk={};
// fill req… then:
if(!OrderCheck(req, chk)) { /* GetLastError + chk.retcode/comment */ }
if(!OrderSend(req, res))  { /* GetLastError */ }
uint rc = res.retcode; // ALWAYS inspect
```

### Required fields (open DEAL)

| Execution | Must set |
|-----------|----------|
| Instant / Request | action, symbol, volume, **price**, sl, tp, deviation, type, type_filling |
| Market / Exchange | action, symbol, volume, type, type_filling (+ magic/comment ok) |

Hedging close/modify: **`position` ticket required**. Netting: one position per symbol.

### Filling × execution

| | FOK | IOC | RETURN |
|--|-----|-----|--------|
| Instant / Request | always | always | always |
| Market | symbol flags | symbol flags | **disabled** |
| Exchange | symbol flags | symbol flags | always |

Pending → **RETURN**. BOC only Limit/StopLimit. Flags: FOK=1, IOC=2, BOC=4.

### Before send

Check `SYMBOL_TRADE_EXEMODE`, `SYMBOL_FILLING_MODE`, `SYMBOL_TRADE_MODE`, `VOLUME_*`, `TRADE_STOPS_LEVEL`, `FREEZE_LEVEL`, `TRADE_TICK_SIZE`, `DIGITS`.

Stops: BUY exits use **Bid**; SELL use **Ask**.

### History

1. `HistorySelect(from,to)` loads cache
2. Iterate deal/order tickets
3. `HistoryDealSelect` / `HistoryOrderSelect` **replace** cache with one item

### OnTradeTransaction

`REQUEST` type: read `request` + `result` (and `request_id` for async). Arrival order not guaranteed. Queue 1024.

Common retcodes: `10009 DONE`, `10030 INVALID_FILL`, `10016 INVALID_STOPS`, `10019 NO_MONEY`, `10027 CLIENT_DISABLES_AT`.

---

## 4. Indicator playbook

Docs: https://www.mql5.com/en/docs/customind · `oncalculate.htm`, `drawstyles.htm`

- Exactly **one** `OnCalculate` overload (applied-price **or** OHLC).
- Return → next `prev_calculated`. **Return 0** → blank DataWindow.
- History change → `prev_calculated=0`.
- Set `ArraySetAsSeries` yourself.
- Buffers: DATA / COLOR_INDEX / CALCULATIONS; color buffers **after** value buffers.
- **No trade, no WebRequest, no Sleep.**

```cpp
int OnCalculate(const int rates_total, const int prev_calculated,
                const datetime &time[], const double &open[],
                const double &high[], const double &low[],
                const double &close[], const long &tick_volume[],
                const long &volume[], const int &spread[]) {
  if(rates_total < need) return 0;
  int start = (prev_calculated > 1) ? prev_calculated - 1 : 0;
  for(int i = start; i < rates_total && !IsStopped(); ++i) { /* … */ }
  return rates_total;
}
```

---

## 5. Series & handles

Docs: https://www.mql5.com/en/docs/series · `series.htm`, `icustom.htm`

- Series index 0 = current bar in series mode.
- Indicators: `Copy*` fails immediately if not ready. EA/scripts: brief retries.
- Prefer static buffers on hot path; `IndicatorRelease` unused handles.

### iCustom

- `"\\Name"` → `MQL5\\Name.ex5`
- else caller folder, then `MQL5\\Indicators\\`
- Applied price / handle = **last** arg after custom inputs
- Non-constant name → `#property tester_indicator`
- Fail → `INVALID_HANDLE` / 4802

---

## 6. Panel & UI playbook

Docs: `enum_chartevents.htm`, `objects.htm`, Controls StdLib

```cpp
ChartSetInteger(0, CHART_EVENT_MOUSE_MOVE, true);
ChartRedraw();
```

| Event | lparam | dparam | sparam |
|-------|--------|--------|--------|
| OBJECT_CLICK | X | Y | name |
| CLICK | X | Y | — |
| OBJECT_DRAG / ENDEDIT | — | — | name |
| KEYDOWN/KEYUP | key | repeats | modifiers |
| MOUSE_MOVE | X | Y | button mask |
| CUSTOM+N | custom | custom | custom |

Object/chart property sets are **async** → `ChartRedraw()`.

Architecture: `Constants → Types → Helpers → Panel` and `Engine ← Orders ← Drawing`.

- Split UI vs chart-line prefixes
- Button click → clear `OBJPROP_STATE`
- Never write `OBJPROP_TEXT` while editing
- Reverse-iterate deletes
- Rich UI: `CAppDialog` + Controls; thin UI: raw `OBJ_*`

---

## 7. Calendar · files · network

### Calendar

Time: **TimeTradeServer**. Values ×1e6 or `LONG_MIN` → `Has*/Get*Value()`.

### Files

Sandbox: `MQL5\\Files\\` or Common (`FILE_COMMON`).

### Network

`WebRequest`: EA/script only; URL allow-list; sync; **not in tester**; indicator → **4014**.

---

## 8. Errors & retcodes

- Runtime 4xxx: `GetLastError` / `_LastError`
- Trade 10004+: `MqlTradeResult.retcode` — separate namespace

| Runtime | Meaning |
|---------|---------|
| 4014 | Not allowed in this program type |
| 4024 | Invalid handle |
| 4108 | Timer create failed |
| 4301/4302 | Symbol unknown / not selected |
| 4401–4407 | History not ready |
| 4802/4806 | Indicator create / copy |

---

## 9. Tester & optimization

Docs: `testing.htm`, `ontester.htm`

**Not in tester:** Alert, MessageBox, PlaySound, SendFTP, SendMail, SendNotification, WebRequest.

**Optimization:** Print/Comment/PrintFormat skipped except in `OnInit`.

Tick modes: Every tick · 1 Minute OHLC · Open prices only.

Cloud: ≤4GB RAM and ≤4GB disk/pass — use frames:

```cpp
bool file_ok = !(MQLInfoInteger(MQL_OPTIMIZATION) || MQLInfoInteger(MQL_FORWARD));
```

`OnTester()` custom criterion: **higher = better**. Frames: `FrameAdd` → `OnTesterPass`; drain in `OnTesterDeinit`.

---

## 10. Compile & ship checklist

1. Correct program type (no trade/Sleep/WebRequest in indicator)?
2. Filling from symbol; pending RETURN?
3. Prices tick-normalized; stops vs stops/freeze?
4. Hedge tickets? HistorySelect before history reads?
5. Handles released? Timers killed? Reverse object deletes?
6. F7 → 0 errors; `.ex5` updated
7. Log: retcode, comment, FILLING_MODE, TRADE_EXEMODE

```cpp
double NormalizePrice(double price) {
  double tick = SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_SIZE);
  if(tick > 0.0) price = MathRound(price / tick) * tick;
  return NormalizeDouble(price, (int)SymbolInfoInteger(_Symbol, SYMBOL_DIGITS));
}
```

---

## 11. Official atlas (open on demand)

| Area | Docs | Corpus stems |
|------|------|----------------|
| Language | /en/docs/basis | `syntax`, `types`, `oop`, `inputvariables` |
| Events | /en/docs/event_handlers | `oninit`, `ontick`, `oncalculate`, `ontradetransaction` |
| Trade | /en/docs/trading | `ordersend`, `historyselect`, `mqltraderequest` |
| Series | /en/docs/series | `series`, `icustom`, `copybuffer` |
| Indicators | /en/docs/customind | `drawstyles` |
| Chart/Objects | chart_operations / objects | `enum_chartevents` |
| Calendar | /en/docs/calendar | calendar value/history |
| Files | /en/docs/files | `filefunctions` |
| Network | /en/docs/network | `webrequest` |
| Database | /en/docs/database | `databaseexecute` |
| Matrix | /en/docs/matrix | `matrix_vector` |
| StdLib | /en/docs/standardlibrary | Trade / Controls / Expert (**index only**) |
| Constants | /en/docs/constants | `marketinfoconstants`, `errorcodes` |
| Testing | Testing section | `testing`, `ontester` |

Do **not** dump entire StdLib or indicator catalogs — open the specific page.

---

## 12. Verified claim set

Non-negotiable (checked vs Reference/CHM): language deltas; 63-char ids; packed structs; 8-byte descriptors; new=classes only; complex DLL by ref; ≤64 params; switch int/uint; fatal OOB/div0/pointer; IsStopped; NormalizeDouble compare; short-circuit bool; `@` matmul; #property main file only; 4D arrays; Init retcodes; WebRequest 4014 + no tester; RETURN vs Market; pending RETURN; HistorySelect cache reset; calendar ×1e6; tester bans + Print-in-opt + Cloud 4GB; tick modes; ChartRedraw; BookEvent rules; OnCalculate return/prev; FOK/IOC/BOC; retcode 10009/10030; tx queue 1024; globals ~4 weeks; 1 EA+1 script/chart; sinput; OrderCheck ≠ guarantee; POSITION_IDENTIFIER vs ticket; color buffers after data; file sandbox; Sleep forbidden in indicators.

If memory conflicts with this list → trust Reference/corpus.

---

## Exness

Same MQL5 rules. Validate live symbol filling / execution / stops.
Intro: https://one.toexness.com/intl/zh/a/30852

