---
name: mql5-workshop
description: "Docs-aligned MQL5 workshop for EAs, indicators, scripts, services, panel UIs, trading (CTrade/OrderSend), compilation, and official API lookup. Prefer https://www.mql5.com/en/docs (also zh/docs). Use for ANY MQL5 coding, debugging, compiling, or retcode/error research."
---

# MQL5 Workshop

Aligned with the official [MQL5 Reference](https://www.mql5.com/en/docs): application types, event handlers, trading structures, Standard Library `CTrade`, and network rules.

Chinese mirror: [https://www.mql5.com/zh/docs](https://www.mql5.com/zh/docs)

---

## Quick Navigation

| Section | For... |
|---------|--------|
| [1. General MQL5 Development](#1-general-mql5-development) | App types, events, project layout, gotchas |
| [2. Indicator Development](#2-indicator-development) | Buffers, OnCalculate, scale, templates |
| [3. Panel EA Patterns](#3-panel-ea-patterns) | Chart UI, drag, multi-TP, trade helpers |
| [4. Compilation & Build](#4-compilation--build) | MetaEditor F7 / Wine |
| [5. Docs Research](#5-docs-research) | Official doc workflow + key anchors |
| [Quick Reference](#quick-reference) | Snippets + common trade retcodes |

---

## 1. General MQL5 Development

### Application Types (official)

Per [MQL5 Reference](https://www.mql5.com/en/docs):

| Type | Bound to chart? | Main handler | Folder |
|------|-----------------|--------------|--------|
| Expert Advisor | Yes | `OnInit`/`OnDeinit`/`OnTick`/`OnTrade`/`OnTradeTransaction`/`OnTimer`/`OnChartEvent`… | `MQL5/Experts` |
| Custom Indicator | Yes | `OnInit`/`OnDeinit`/`OnCalculate` (+ optional timer/chart events) | `MQL5/Indicators` |
| Script | Yes (one-shot) | `OnStart` only | `MQL5/Scripts` |
| Service | **No** | `OnStart` only; can `EventChartCustom` | `MQL5/Services` |
| Library | — | No event handlers | `MQL5/Libraries` |
| Include | — | Compiled into callers (`#include`) | `MQL5/Include` (or beside source with `"file.mqh"`) |

Prefer `#include <...>` from `Include/` over Libraries when possible (less call overhead per docs).

### Event Handling (official)

Handlers must match documented signatures exactly or they will not be wired. See [Event Handling](https://www.mql5.com/en/docs/event_handlers).

Queue rules that matter in practice:

- Programs only receive events from **their** chart (except Services).
- Events are processed **one-by-one**; do **not** use infinite loops in EAs/indicators.
- If a `NewTick`, `ChartEvent`, or `Timer` event is already queued or being handled, a **new duplicate of that type is discarded**.
- Queue overflow drops new events — keep handlers short.

| Handler | Typical use |
|---------|-------------|
| `OnInit` / `OnDeinit` | Setup / teardown (EA + indicator) |
| `OnTick` | EA quote-driven logic |
| `OnCalculate` | Indicator recalculation |
| `OnTimer` | Periodic UI/trade maintenance (`EventSetTimer` / `EventSetMillisecondTimer`) |
| `OnTrade` / `OnTradeTransaction` | React to server-side trade results |
| `OnChartEvent` | Panel clicks, object drag, keys |
| `OnTester*` | Strategy Tester / optimization hooks |

### MQL4 vs MQL5 Key Differences

| Aspect | MQL4 | MQL5 |
|--------|------|------|
| Paradigm | Procedural (C-like) | OOP (C++-like) + Standard Library |
| Trade model | Ticket orders (`OrderSend` MQL4) | Requests: Orders + Deals + Positions (`OrderSend` + `MqlTradeRequest` / `CTrade`) |
| Account model | Hedging-oriented | Netting **and** Hedging (`ACCOUNT_MARGIN_MODE`) |
| Indicator buffers | Max 8 | Max 512 |
| Events | Fewer | Rich predefined set (trade transaction, book, tester…) |

### Critical Gotchas

- **Double comparison**: Never use `==`/`!=` for live prices. Use tick-size rounding + `NormalizeDouble`, or epsilon. Exception: exact `EMPTY_VALUE` sentinel after you wrote `EMPTY_VALUE`.
- **Digits / pip**: Use `SYMBOL_DIGITS` / `_Point`. On 3/5-digit FX symbols, 1 pip is typically `10 * _Point`.
- **Stops level / freeze**: Validate SL/TP against `SYMBOL_TRADE_STOPS_LEVEL`, `SYMBOL_TRADE_FREEZE_LEVEL`, and tick size before send.
- **Reverse loop when deleting/closing by index**: Closing/deleting shifts indexes — iterate high → low.
- **Market execution / ECN**: Some symbols reject SL/TP on the opening market request — open first, then `PositionModify` / `OrderModify` (broker/symbol dependent).
- **Filling policy**: Choose `type_filling` from symbol flags + execution mode. Prefer `CTrade::SetTypeFillingBySymbol()`. Pending orders should use `ORDER_FILLING_RETURN`. See [symbol filling](https://www.mql5.com/en/docs/constants/environment_state/marketinfoconstants).
- **WebRequest**: Synchronous. **Only EAs and scripts** (not indicators — error `4014`). URL must be allow-listed. **Cannot run in Strategy Tester**. Docs: [WebRequest](https://www.mql5.com/en/docs/network/webrequest).
- **Array series**: `ArraySetAsSeries(true)` → index `0` is newest bar; keep buffer series mode consistent with `CopyBuffer` usage.
- **Trade results**: After `OrderSend` / `CTrade`, trust `MqlTradeResult.retcode` / `ResultRetcode()`, not a random `GetLastError()` pairing.

### Project Structure

```
MQL5/
├── Experts/MyEA/MyEA.mq5
├── Indicators/MyIndicator.mq5
├── Scripts/MyScript.mq5
├── Services/MyService.mq5
├── Include/
│   ├── Trade/          # or your own trade wrappers
│   ├── UI/
│   └── Utils/
└── Libraries/          # compiled .ex5 libraries (optional)
```

### Code Style

- Members `m_`, globals `g_`
- User inputs: `input` (not MQL4 `extern` for parameters)
- Prices: round to `SYMBOL_TRADE_TICK_SIZE`, then `NormalizeDouble(..., digits)`
- Always check trade `retcode` (`TRADE_RETCODE_DONE` / `DONE_PARTIAL` / `PLACED`, etc.)

---

## 2. Indicator Development

### Display Scale

**Problem**: Blank indicator window for small values (< 1.0 range).

**Fix**: Always set explicit scale in `OnInit()`:

```cpp
IndicatorSetDouble(INDICATOR_MINIMUM, 0.0);
IndicatorSetDouble(INDICATOR_MAXIMUM, 0.1);
```

Required for any indicator whose values span a range smaller than 1.0 (scores, probabilities, normalized metrics).

### Buffer Architecture

**Two-buffer pattern** (visible + hidden):

```cpp
#property indicator_buffers 2
#property indicator_plots   1

double BufVisible[];  // Plot buffer
double BufHidden[];   // Tracking buffer

int OnInit() {
   SetIndexBuffer(0, BufVisible, INDICATOR_DATA);        // Visible
   SetIndexBuffer(1, BufHidden, INDICATOR_CALCULATIONS); // Hidden
   return INIT_SUCCEEDED;
}
```

Buffer types:
- `INDICATOR_DATA` — visible plot on chart
- `INDICATOR_CALCULATIONS` — hidden, for internal tracking

### Bar Recalculation (Rolling Window)

**Problem**: Current bar updates tick-by-tick — naive implementations double-count values.

**Solution** — new bar detection + value replacement:

```cpp
static double sum = 0.0;
static int last_processed_bar = -1;

int OnCalculate(...) {
   for(int i = start; i < rates_total; i++) {
      bool is_new_bar = (i > last_processed_bar);
      double current = GetValue(i);

      if(is_new_bar) {
         if(i >= window_size) {
            int idx_out = i - window_size;
            sum -= BufHidden[idx_out];
         }
         sum += current;
      } else {
         if(i == last_processed_bar && BufHidden[i] != EMPTY_VALUE) {
            sum -= BufHidden[i];
         }
         sum += current;
      }

      BufHidden[i] = current;
      last_processed_bar = i;
      BufVisible[i] = sum / window_size;
   }
   return rates_total;
}
```

### Complete Indicator Template

```cpp
#property indicator_separate_window
#property indicator_buffers 2
#property indicator_plots   1
#property indicator_label1    "My Indicator"
#property indicator_type1     DRAW_LINE
#property indicator_color1    clrOrange
#property indicator_width1    2

input int InpPeriod = 20;
input int InpWindow = 30;

double BufVisible[];
double BufHidden[];
int hBase = INVALID_HANDLE;

int OnInit() {
   SetIndexBuffer(0, BufVisible, INDICATOR_DATA);
   SetIndexBuffer(1, BufHidden, INDICATOR_CALCULATIONS);

   int StartCalcPosition = InpPeriod + InpWindow - 1;
   PlotIndexSetInteger(0, PLOT_DRAW_BEGIN, StartCalcPosition);
   PlotIndexSetDouble(0, PLOT_EMPTY_VALUE, EMPTY_VALUE);

   IndicatorSetDouble(INDICATOR_MINIMUM, 0.0);
   IndicatorSetDouble(INDICATOR_MAXIMUM, 1.0);

   hBase = iSomeIndicator(_Symbol, _Period, InpPeriod);
   if(hBase == INVALID_HANDLE) return INIT_FAILED;
   return INIT_SUCCEEDED;
}

void OnDeinit(const int reason) {
   if(hBase != INVALID_HANDLE) IndicatorRelease(hBase);
}

int OnCalculate(const int rates_total, const int prev_calculated,
                const datetime &time[], ...) {
   int StartCalcPosition = InpPeriod + InpWindow - 1;
   if(rates_total <= StartCalcPosition) return 0;

   static double base[];
   ArrayResize(base, rates_total);
   ArraySetAsSeries(base, false);
   if(CopyBuffer(hBase, 0, 0, rates_total, base) < rates_total)
      return prev_calculated;

   ArraySetAsSeries(BufVisible, false);
   ArraySetAsSeries(BufHidden, false);

   int start = (prev_calculated == 0) ? StartCalcPosition : prev_calculated - 1;
   if(start < StartCalcPosition) start = StartCalcPosition;

   if(prev_calculated == 0) {
      for(int i = 0; i < start; i++) {
         BufVisible[i] = EMPTY_VALUE;
         BufHidden[i] = EMPTY_VALUE;
      }
   }

   static double sum = 0.0;
   static int last_processed_bar = -1;

   if(prev_calculated == 0 || start == StartCalcPosition) {
      sum = 0.0;
      last_processed_bar = StartCalcPosition - 1;
      for(int j = start - InpWindow + 1; j <= start; j++)
         sum += base[j];
   }

   for(int i = start; i < rates_total && !IsStopped(); i++) {
      bool is_new_bar = (i > last_processed_bar);
      if(is_new_bar && i >= InpWindow) {
         int idx_out = i - InpWindow;
         sum -= base[idx_out];
      }
      double current = base[i];
      if(is_new_bar) {
         sum += current;
      } else {
         if(i == last_processed_bar && BufHidden[i] != EMPTY_VALUE)
            sum -= BufHidden[i];
         sum += current;
      }
      last_processed_bar = i;
      BufHidden[i] = current;
      BufVisible[i] = sum / InpWindow;
   }
   return rates_total;
}
```

### Indicator Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| Blank window | Scale not set for small values | Set INDICATOR_MINIMUM/MAXIMUM |
| Values drifting | Rolling window not reset | Use new bar detection + hidden buffer |
| Misaligned plot | Wrong PLOT_DRAW_BEGIN | Calculate: underlying_warmup + own_warmup |
| Reversed indexing | Series mode enabled | `ArraySetAsSeries(buffer, false)` |
| Buffer values wrong | Wrong INDICATOR_DATA type | Use INDICATOR_CALCULATIONS for hidden |
| Compile error on buffer | Buffer count mismatch | Match `indicator_buffers` with SetIndexBuffer calls |
| Not updating | OnCalculate return wrong | Return `rates_total` to signal success |
| Performance issue | Recalculating all bars | Only recalculate from `prev_calculated` |

### Debugging Checklist

1. **Scale**: Added `IndicatorSetDouble(INDICATOR_MINIMUM/MAXIMUM)`? Range appropriate?
2. **Buffers**: `indicator_buffers >= indicator_plots`? Hidden buffers for old values? `ArraySetAsSeries(buffer, false)`?
3. **Warmup**: `PLOT_DRAW_BEGIN` correct? Early bars initialized to `EMPTY_VALUE`?
4. **Recalculation**: `is_new_bar` logic? Old value subtracted before adding new? `last_processed_bar` tracking?
5. **Data flow**: Base handle valid? `CopyBuffer` returning expected count? No `EMPTY_VALUE` in calculated range?

---

## 3. Panel EA Patterns

### Module Architecture

```
Constants.mqh → Types.mqh → Helpers.mqh → Panel_Int.mqh → Panel.mqh
                                                              ↓
                               Orders.mqh ← Engine.mqh ← Drawing.mqh
```

Include order matters. Types before helpers before panel. Engine depends on Orders.

### Global State Pattern

Each include module binds to globals declared in the main EA:

```cpp
// In main EA
PanelConfig m_cfg;
PanelState  m_state;

// In each .mqh
extern PanelConfig  m_cfg;
extern PanelState   m_state;
```

### Naming Conventions

| Category | Prefix | Example |
|----------|--------|---------|
| Panel objects | `TP_` | `TP_ed_entry`, `TP_btn_buy` |
| Chart lines | `HL_ENTRY_`, `HL_SL_`, `HL_TP_` | `HL_ENTRY_0` |
| TREND handles | `HL_TRE_EN_`, `HL_TRE_SL_`, `HL_TRE_TP_` | `HL_TRE_EN_0` |
| Label backgrounds | `TP_LBL_XX_BG` | `TP_LBL_TP_BG` |
| Label text | `TP_LBL_XX_TX` | `TP_LBL_TP_TX` |

Chart line prefixes **must differ** from panel object prefixes so bulk delete doesn't destroy chart lines.

### Pip / Point Handling

```cpp
int digits = (int)SymbolInfoInteger(_Symbol, SYMBOL_DIGITS);
g_pipSize = (digits == 3 || digits == 5) ? g_point * 10.0 : g_point;

double TM_NormalizePrice(double price) {
    int digits = (int)SymbolInfoInteger(_Symbol, SYMBOL_DIGITS);
    double tickSize = SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_SIZE);
    if(tickSize > 0)
        price = MathRound(price / tickSize) * tickSize;
    return NormalizeDouble(price, digits);
}
```

### Edit Field Safety

**Never** call `ObjectSetString(..., OBJPROP_TEXT, ...)` on an edit field during active editing:

```cpp
void TM_SafeSetEdit(string name, string text) {
    if(m_state.activeEditField == name)
        return;
    ObjectSetString(0, name, OBJPROP_TEXT, text);
}
```

Track `activeEditField` in `CHARTEVENT_OBJECT_BEGINEDIT` and clear in `CHARTEVENT_OBJECT_ENDEDIT`.

### OBJ_BUTTON Click Handling

Prefer handling `CHARTEVENT_OBJECT_CLICK` once, then reset sticky pressed state:

```cpp
if(id == CHARTEVENT_OBJECT_CLICK) {
    if(ObjectGetInteger(0, sparam, OBJPROP_TYPE) == OBJ_BUTTON) {
        ObjectSetInteger(0, sparam, OBJPROP_STATE, false); // unstick visual press
        // handle action for sparam once here
    }
}
```

Avoid fragile mouse-down/up latches that can swallow legitimate clicks.

### Panel Drag via MOUSE_MOVE

Do NOT rebuild panel during drag — shift all objects by delta:

```cpp
void TM_ShiftAllByDelta(int dx, int dy) {
    for(int i = ObjectsTotal(0) - 1; i >= 0; i--) {
        string name = ObjectName(0, i);
        if(TM_StringStartsWith(name, TP_PREFIX))
            TM_ShiftObject(name, dx, dy);
    }
}
void TM_ShiftObject(const string name, int dx, int dy) {
    int x = (int)ObjectGetInteger(0, name, OBJPROP_XDISTANCE);
    int y = (int)ObjectGetInteger(0, name, OBJPROP_YDISTANCE);
    ObjectSetInteger(0, name, OBJPROP_XDISTANCE, x + dx);
    ObjectSetInteger(0, name, OBJPROP_YDISTANCE, y + dy);
}
```

Use `CHARTEVENT_MOUSE_MOVE` with bit 0 of `sparam`. Save position to `GlobalVariableSet` per ChartID on drag-end.

### Chart Line Drag Pattern

HLINE/TREND drag has three phases:
1. **DRAG start / during drag**: `CHARTEVENT_OBJECT_DRAG` → read price with `ObjectGetDouble()` and update UI live
2. **Optional fine refresh**: short `OnTimer` (e.g. 100–250 ms) only if needed; **do not** use 1s polling as the primary path
3. **Drag end**: `CHARTEVENT_OBJECT_CHANGE` (or final DRAG) → apply/normalize SL/TP and send trade modify if required

### String Helpers (MQL5 compat)

```cpp
bool TM_StringStartsWith(const string &text, const string &prefix) {
    if(StringLen(text) < StringLen(prefix)) return false;
    return StringCompare(StringSubstr(text, 0, StringLen(prefix)), prefix) == 0;
}
```

Always iterate objects in **reverse** when deleting:

```cpp
void TM_DeleteByPrefix(const string prefix) {
    for(int i = ObjectsTotal(0) - 1; i >= 0; i--) {
        string name = ObjectName(0, i);
        if(TM_StringStartsWith(name, prefix))
            ObjectDelete(0, name);
    }
}
```

### Config Persistence

```cpp
void TM_SaveConfig(const PanelConfig &cfg) {
    int h = FileOpen("Config.dat", FILE_WRITE | FILE_BIN);
    if(h == INVALID_HANDLE) return;
    FileWriteStruct(h, cfg);
    FileClose(h);
}
bool TM_LoadConfig(PanelConfig &cfg) {
    int h = FileOpen("Config.dat", FILE_READ | FILE_BIN);
    if(h == INVALID_HANDLE) return false;
    bool ok = FileReadStruct(h, cfg);
    FileClose(h);
    return ok;
}
```

### Position Iteration

Always check `ticket == 0` after `PositionGetTicket()`:

```cpp
for(int i = 0; i < PositionsTotal(); i++) {
    ulong ticket = PositionGetTicket(i);
    if(ticket == 0) continue;
    if(PositionGetString(POSITION_SYMBOL) != _Symbol) continue;
    if(PositionGetInteger(POSITION_MAGIC) != TP_MAGIC) continue;
}
```

### Trade Object Init (Standard Library)

Docs: [CTrade](https://www.mql5.com/en/docs/standardlibrary/tradeclasses/ctrade), especially [`SetTypeFillingBySymbol`](https://www.mql5.com/en/docs/standardlibrary/tradeclasses/ctrade/ctradesettypefillingbysymbol).

```cpp
#include <Trade/Trade.mqh>
CTrade g_trade;

void TM_OrdersInit() {
    g_trade.SetExpertMagicNumber(TP_MAGIC);
    g_trade.SetDeviationInPoints(30);
    // Official helper: picks allowed filling from SYMBOL_FILLING_MODE
    // (if both FOK+IOC allowed, CTrade selects FOK)
    if(!g_trade.SetTypeFillingBySymbol(_Symbol))
        Print("SetTypeFillingBySymbol failed for ", _Symbol);
}
```

Manual rules (when not using `CTrade`):

- Read `SYMBOL_FILLING_MODE` flags (`FOK=1`, `IOC=2`, `BOC=4`).
- Also respect `SYMBOL_TRADE_EXEMODE`: under **Market execution**, `ORDER_FILLING_RETURN` is **disabled**.
- For **pending** orders, docs say use **`ORDER_FILLING_RETURN`** regardless of execution mode (pending is not filled at send time).
- Invalid filling → `TRADE_RETCODE_INVALID_FILL` (**10030**).

### Pending Order / Stops Compatibility

Check `MqlTradeResult.retcode` ([trade return codes](https://www.mql5.com/en/docs/constants/errorswarnings/enum_trade_return_codes)):

```cpp
#include <Trade/Trade.mqh>
CTrade trade;
// ... Place order via trade.BuyStop / OrderOpen / OrderSend ...
uint rc = trade.ResultRetcode();
if(rc != TRADE_RETCODE_DONE && rc != TRADE_RETCODE_PLACED && rc != TRADE_RETCODE_DONE_PARTIAL) {
    PrintFormat("Trade failed retcode=%u %s | %s",
                rc, trade.ResultRetcodeDescription(), trade.ResultComment());
    if(rc == TRADE_RETCODE_INVALID_STOPS) {
        // Broker rejected SL/TP on this request — retry without stops, then modify after fill
    }
    if(rc == TRADE_RETCODE_INVALID_FILL) {
        // Fix type_filling via SetTypeFillingBySymbol / SYMBOL_FILLING_MODE
    }
}
```

### SL/TP Wrong-Side Validation

```cpp
bool TM_ValidateSLTP(ENUM_TRADE_DIR dir, double entry, double sl, double tp) {
    if(dir == TD_BUY) {
        if(sl > 0.0 && sl >= entry) return false;
        if(tp > 0.0 && tp <= entry) return false;
    } else {
        if(sl > 0.0 && sl <= entry) return false;
        if(tp > 0.0 && tp >= entry) return false;
    }
    return true;
}
```

### 3-TP Architecture

TP1 is broker-level. TP2/TP3 tracked locally:

```cpp
void TM_CheckMultiTP() {
    for(int i = 0; i < 3; i++) {
        if(!m_cfg.tpLevels[i].isActive) continue;
        if(m_cfg.tpLevels[i].isHit) continue;
        double targetDist = m_cfg.tpLevels[i].pipsDist * g_pipSize;
        // BUY closes on Bid; SELL closes on Ask
        bool hit = (posType == POSITION_TYPE_BUY)
            ? ((bid - entry) >= targetDist)
            : ((entry - ask) >= targetDist);
        if(hit) {
            double closeVol = TM_NormalizeLot(vol * m_cfg.tpLevels[i].allocPct / 100.0);
            g_engineTrade.PositionClosePartial(ticket, closeVol);
            m_cfg.tpLevels[i].isHit = true;
        }
    }
}
```

### Multi-TP Allocation Cycling

Keep active levels summing to exactly 100%:

```cpp
void TM_EnsureAlloc100() {
    int active = 0, sum = 0;
    for(int i = 0; i < 3; i++) {
        if(m_cfg.tpLevels[i].isActive) { active++; sum += m_cfg.tpLevels[i].allocPct; }
        else m_cfg.tpLevels[i].allocPct = 0;
    }
    if(sum == 100) return;
    if(sum == 0) { return; }
    int newSum = 0;
    for(int i = 0; i < 3; i++) {
        if(!m_cfg.tpLevels[i].isActive) continue;
        int scaled = (int)MathRound((double)m_cfg.tpLevels[i].allocPct * 100.0 / sum);
        m_cfg.tpLevels[i].allocPct = scaled;
        newSum += scaled;
    }
    for(int i = 2; i >= 0; i--)
        if(m_cfg.tpLevels[i].isActive) { m_cfg.tpLevels[i].allocPct += 100 - newSum; break; }
}
```

### RR Lock (Forward/Reverse)

When RR locked, dragging SL recalculates TP (forward), dragging TP recalculates SL (reverse):

```cpp
// Forward: SL -> TP
double tpDist = slDist * m_cfg.rrLockedValue;
double newTp  = (tradeDir == TD_BUY) ? entry + tpDist : entry - tpDist;

// Reverse: TP -> SL
double slDist = tpDist / m_cfg.rrLockedValue;
double newSl  = (tradeDir == TD_BUY) ? entry - slDist : entry + slDist;
```

### Prop Firm Config Backup

```cpp
// Before entering prop firm screen:
m_state.m_cfgBackup = m_cfg;
TM_BuildPropFirmScreen();

// On "Back":
m_cfg = m_state.m_cfgBackup;
TM_BuildPanel();
```

### Color Definitions (Dark Theme)

Use `C'R,G,B'` syntax (not hex):

```cpp
#define CLR_SURFACE_BASE    C'13,13,26'    // #0D0D1A
#define CLR_SURFACE_PANEL   C'21,21,33'    // #151521
#define CLR_SURFACE_RAISED  C'28,28,46'    // #1C1C2E
#define CLR_BUY             C'0,191,165'   // #00BFA5
#define CLR_SELL            C'255,82,82'   // #FF5252
#define CLR_ACCENT          C'108,92,231'  // #6C5CE7
```

### Layout Column System

```cpp
#define PANEL_WIDTH        320
#define ROW_HEIGHT         32
#define HEADER_HEIGHT      36
#define PANEL_PADDING      12
#define COL_LABEL_W        70
#define COL_EDIT_W         70
#define COL_PIPS_W         80
#define COL_BTN_W          60
```

### Timer-Based Label Refresh

Use `EventSetTimer(1)` for 1-second label updates, not OnTick:

```cpp
void OnTimer() {
    if(m_state.isVisible)
        TM_RefreshLabels();
}
```

### Object Label Efficiency

Only set text if changed:

```cpp
void TM_SetText(string name, string text) {
    string cur = ObjectGetString(0, name, OBJPROP_TEXT);
    if(cur != text)
        ObjectSetString(0, name, OBJPROP_TEXT, text);
}
```

### Account Stats & Day Rollover

```cpp
MqlDateTime tm;
TimeCurrent(tm);
int today = tm.year * 10000 + tm.mon * 100 + tm.day;
if(m_state.lastDayOfYear != today) {
    m_state.lastDayOfYear = today;
    m_state.dailyStartBalance = AccountInfoDouble(ACCOUNT_BALANCE);
    m_state.todayTrades = 0;
}
```

### Daily Limit Check

```cpp
if(breachLoss || breachTarget) {
    TM_CloseAllPositions();
    m_state.blocked = true;
}
```

### Panel EA Common Mistakes

| Mistake | Fix |
|---------|-----|
| `plot()` inside local scope | MQL5 is not Pine. Use `ObjectCreate` for chart objects |
| `ObjectSetString` on active edit | Guard with `activeEditField` check |
| Hardcoded `ORDER_FILLING_*` | Prefer `CTrade::SetTypeFillingBySymbol`; pending → `ORDER_FILLING_RETURN` |
| BUY TP checked with Ask | Use Bid for BUY exit/TP distance |
| `WebRequest` in indicator | Forbidden (4014); EA/script only + URL allow-list |
| Ignoring `retcode` | Use `TRADE_RETCODE_*` / `ResultRetcode()` |
| Same prefix for panel + chart lines | Use separate prefixes so bulk delete is safe |
| Rebuilding panel during drag | Shift all objects by delta instead |
| Not iterating in reverse when deleting | Deleted positions shift indices below |
| Using hex `#RRGGBB` | MQL5 uses `C'R,G,B'` syntax |
| SL/TP not normalized to tick size | Always call `TM_NormalizePrice()` before trade functions |
| Not checking `ticket == 0` after `PositionGetTicket` | Returns 0 at end of list |

---

## 4. Compilation & Build

### Windows

**Via MetaEditor GUI**: Open `.mq5` in MetaEditor → **F7** (Compile), or the Compile toolbar button.

**Via build script** (`build.ps1` — tm/ project style): The script compiles `.mq5` files by calling MetaEditor64.exe from the command line.

### macOS (Wine X: Drive)

**Prerequisite** — map X: drive:

```bash
BOTTLE="$HOME/Library/Application Support/net.metaquotes.wine.metatrader5"
cd "$BOTTLE/dosdevices"
ln -s "../drive_c/Program Files/MetaTrader 5/MQL5" "x:"
```

**Compile**:

```bash
WINE="/Applications/MetaTrader 5.app/Contents/SharedSupport/wine/bin/wine64"
WINEPREFIX="$HOME/Library/Application Support/net.metaquotes.wine.metatrader5"
WINEPREFIX="$WINEPREFIX" "$WINE" "C:/Program Files/MetaTrader 5/MetaEditor64.exe" \
    /log /compile:"X:\\Indicators\\Custom\\File.mq5" /inc:"X:"
```

**Critical**: Wine returns exit code 1 even on success. Ignore it — verify `.ex5` file exists and check per-file `.log`.

### Verification

Check `.ex5` file was created with recent timestamp. Check per-file log for "0 errors, 0 warnings". The `.log` file is in the same directory as the `.mq5` file.

---

## 5. Docs Research

### Scope

**Primary**: [https://www.mql5.com/en/docs](https://www.mql5.com/en/docs)  
Also: [zh/docs](https://www.mql5.com/zh/docs), [ja/docs](https://www.mql5.com/ja/docs)

Do **not** invent APIs from forums/blogs when the Reference already defines the function.

### High-value official anchors

| Topic | Doc |
|-------|-----|
| Language overview / app types | https://www.mql5.com/en/docs |
| Event handlers | https://www.mql5.com/en/docs/event_handlers |
| `OnCalculate` | https://www.mql5.com/en/docs/event_handlers/oncalculate |
| Trade request struct | https://www.mql5.com/en/docs/constants/structures/mqltraderequest |
| Trade result / `OrderSend` | https://www.mql5.com/en/docs/trading/ordersend |
| Trade server retcodes | https://www.mql5.com/en/docs/constants/errorswarnings/enum_trade_return_codes |
| Symbol filling / execution | https://www.mql5.com/en/docs/constants/environment_state/marketinfoconstants |
| `CTrade` | https://www.mql5.com/en/docs/standardlibrary/tradeclasses/ctrade |
| `WebRequest` | https://www.mql5.com/en/docs/network/webrequest |
| Chart events | https://www.mql5.com/en/docs/constants/chartconstants/enum_chartevents |
| Runtime errors | https://www.mql5.com/en/docs/constants/errorswarnings/errorcodes |

### Workflow

1. Map the question to a symbol: function name, enum, retcode (`10016`, `10030`…), or handler (`OnTradeTransaction`).
2. Open the matching Reference page (table above first).
3. Confirm signature, required `MqlTradeRequest` fields, and allowed enums.
4. Answer with: cause → doc link → concrete fix → what to log (`retcode`, `comment`, symbol properties).

### Guardrails

- Never invent undocumented overloads or retcodes.
- If docs are ambiguous for a broker quirk, say so and suggest logging `SYMBOL_TRADE_EXEMODE`, `SYMBOL_FILLING_MODE`, `SYMBOL_TRADE_MODE`.
- Keep answers short and actionable.
---

## Quick Reference

### Essential Code Snippets

```cpp
// Pip/point detection
int digits = (int)SymbolInfoInteger(_Symbol, SYMBOL_DIGITS);
double pipSize = (digits == 3 || digits == 5) ? _Point * 10.0 : _Point;

// Price normalization
double NormalizePrice(double price) {
    double tickSize = SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_SIZE);
    if(tickSize > 0) price = MathRound(price / tickSize) * tickSize;
    return NormalizeDouble(price, (int)SymbolInfoInteger(_Symbol, SYMBOL_DIGITS));
}

// New bar detection
static int last_processed_bar = -1;
bool is_new_bar = (i > last_processed_bar);

// SL/TP wrong-side check
bool ValidateSLTP(ENUM_TRADE_DIR dir, double entry, double sl, double tp) {
    if(dir == TD_BUY)  return !(sl > 0 && sl >= entry) && !(tp > 0 && tp <= entry);
    return !(sl > 0 && sl <= entry) && !(tp > 0 && tp >= entry);
}

// Trade init — official filling helper
#include <Trade/Trade.mqh>
CTrade trade;
trade.SetExpertMagicNumber(MAGIC);
trade.SetDeviationInPoints(30);
trade.SetTypeFillingBySymbol(_Symbol);
// Pending orders: use ORDER_FILLING_RETURN (per docs)

// Safe edit field update
if(m_state.activeEditField == name) return;
ObjectSetString(0, name, OBJPROP_TEXT, text);

// Config save/load (terminal Files folder)
int h = FileOpen("cfg.dat", FILE_WRITE | FILE_BIN);
FileWriteStruct(h, cfg); FileClose(h);

// Object reverse delete
for(int i = ObjectsTotal(0)-1; i >= 0; i--)
    if(StringFind(ObjectName(0, i), prefix) == 0)
        ObjectDelete(0, ObjectName(0, i));
```

### Common trade retcodes

| Code | Constant | Meaning |
|------|----------|---------|
| 10008 | `TRADE_RETCODE_PLACED` | Order placed |
| 10009 | `TRADE_RETCODE_DONE` | Done |
| 10010 | `TRADE_RETCODE_DONE_PARTIAL` | Partial fill |
| 10014 | `TRADE_RETCODE_INVALID_VOLUME` | Bad volume |
| 10015 | `TRADE_RETCODE_INVALID_PRICE` | Bad price |
| 10016 | `TRADE_RETCODE_INVALID_STOPS` | Bad SL/TP |
| 10019 | `TRADE_RETCODE_NO_MONEY` | Free margin |
| 10030 | `TRADE_RETCODE_INVALID_FILL` | Bad `type_filling` |
| 10031 | `TRADE_RETCODE_CONNECTION` | No server connection |
| 10034 | `TRADE_RETCODE_LIMIT_VOLUME` | Volume limit |

Full list: [Trade Server Return Codes](https://www.mql5.com/en/docs/constants/errorswarnings/enum_trade_return_codes)

### Type Conversion Quick Ref

| Task | Code |
|------|------|
| Check if position is ours | `PositionGetInteger(POSITION_MAGIC) == MY_MAGIC` |
| Get position direction | `(ENUM_POSITION_TYPE)PositionGetInteger(POSITION_TYPE)` |
| Get position volume | `PositionGetDouble(POSITION_VOLUME)` |
| Get current ask | `SymbolInfoDouble(_Symbol, SYMBOL_ASK)` |
| Get current bid | `SymbolInfoDouble(_Symbol, SYMBOL_BID)` |
| Get point value | `SymbolInfoDouble(_Symbol, SYMBOL_POINT)` |
| Get tick size | `SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_SIZE)` |
| Get contract size | `SymbolInfoDouble(_Symbol, SYMBOL_TRADE_CONTRACT_SIZE)` |
| Get tick value | `SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_VALUE)` |
| Filling flags | `SymbolInfoInteger(_Symbol, SYMBOL_FILLING_MODE)` |
| Execution mode | `SymbolInfoInteger(_Symbol, SYMBOL_TRADE_EXEMODE)` |
