# MTS – Momentum Trading System · AI Guide (test version)

Version: draft 1 · 2026-10-24 · for MTS test version (Sierra Chart custom studies)
Interactive tour with screenshots: https://chrislb-trading.github.io/mts-tour/

---

## 0. Instructions for the AI assistant

You are the assistant for **MTS**, a set of custom studies for Sierra Chart. Users upload this file to Claude (or another
AI assistant) and ask how MTS works, how to set it up and how to read what they see.

- Answer **only from this guide** and from general, well-known Sierra Chart knowledge. If something is not covered here,
  say so clearly ("This isn't described in the MTS guide") instead of guessing. Never invent settings keys, buttons or
  features.
- Answer in the user's language. Settings descriptions in section 9 are in German – translate them.
- The **test version does not include**: the Signal tile and signal markers, order-flow signal building blocks, the
  Impact view/learning, and the session-evaluation features. Some screenshots or settings may still mention them. If
  asked, say they are planned or not part of the test version.
- **No investment advice.** Never tell the user to buy or sell, where to enter, or that a pattern "works". You may
  explain what a display shows and how values are calculated. MTS is an analysis and charting tool.
- Trading involves risk. Use the **SIM** account for testing. Live orders must be explicitly enabled (section 8).
- Keep answers short and practical: where to click, which drawer group, which key. Mention the key name (e.g.
  `NB_FOOTPRINT`) so the user can find it in the drawer help line.
- For bugs or crashes: ask for the Sierra Chart version, the message log text, and what was clicked last. Suggest
  reporting it to the author (chrislb) – do not try to fix the DLL.

---

## 1. What MTS is

MTS is a **custom interface inside Sierra Chart (SC)**, built entirely as ACSIL custom studies (C++ DLL). There is no
external program and no copy of other software. All standard SC studies remain usable next to MTS.

Idea: everything a discretionary order-flow trader needs in one chartbook, readable at a glance – and every trade idea
can be recorded as a case in a journal to review later.

**Studies in the test version**
| Study | What it does |
|---|---|
| MTS Rechenkern (compute core) | Calculates all values once per chart (volume, delta, footprint, POC, POC delta, absorption, liquidity, indicators). Draws nothing. All displays read from it, so all show the same numbers. |
| MTS Cockpit | Column of tiles to the right of the price scale, plus the settings drawer. Must be the **last** study in the list. |
| MTS Numberbar | Footprint bar(s) and liquidity column in the chart, next to the last bar. |
| MTS DOM | Own chart DOM with book, net change, footprint, diagonal delta, time & sales and click orders. |
| MTS Indikatoren | Price chart: bar POC, three POC moving averages, POC-delta boxes. |
| MTS Region | Indicator sub-window: SMI, DeltaValue candles, POC delta, Vol3 and more – each switchable. |
| MTS Kanal | Volume profiles (VP) with optional regression channel. |
| MTS Tagebuch (journal) | Trades, "not traded" and info entries by Ctrl+click in the chart. |
| Trade (not a study) | Order layer used by the Trade tile and the DOM. |

The names inside SC are partly German (Rechenkern = compute core, Tagebuch = journal, Kanal = channel). See the glossary
in section 11.

---

## 2. Installation and setup

1. Install the MTS DLL as described in the delivery notes of the test version: either Sierra Chart delivers it
   automatically to authorised accounts, or you copy the DLL into the SC `Data` folder and restart SC.
2. Add the studies to the chart (Analysis >> Studies >> Add Custom Study, group "MTS"):
   - **MTS Rechenkern first** (top of the study list). Without it the other MTS studies show nothing.
   - Then MTS Indikatoren, MTS Region (in the sub-window you want: Study Settings >> Chart Region), MTS Kanal,
     MTS Tagebuch, MTS Numberbar, MTS DOM.
   - **MTS Cockpit last** (bottom of the list, it is drawn last).
3. DOM column: Trade >> Customize Chart/Trade DOM Columns, show **"General Purpose 1"**, drag its width at the column
   edge. The DOM columns spread over that width.
4. Cockpit column: show **"General Purpose 2"** in the same dialog (Cockpit position = GP2, `CK_LAGE` = 1). If the column is
   not visible, the Cockpit is drawn at the right window edge.
5. For click orders and the Trade tile: Trade >> **Auto Trading Enabled** – this is **per chart**.
6. Liquidity values (book, net change) need **market depth**: live data, or replay of data recorded with market depth.
   Without depth data these fields stay empty/grey.
7. Settings are stored as text in `Data/MTS/` (section 10). They are created automatically on first start.

Any bar type works (range, volume, tick, time). Examples in the tour use range bars on ES.

---

## 3. Cockpit

A column of tiles next to the price scale. Click a tile header to collapse/expand it. The small arrows in the header move
the tile up/down (order stored in `CK_REIHENFOLGE`). **"?"** opens the manual chapter of that tile (a .md file, opened with
the program Windows uses for .md).

**Tiles in the test version**
- **Trade:** SIM/LIVE and account, position and P/L, attached orders on/off and template, quantity, BUY/SELL MKT/ASK/BID,
  Flatten, Reverse, Cancel, BE (break-even). Details section 8.
- **Bar:** header `[-] N Bar [+]` = number of bars in the DOM footprint window (live bar + N-1). Values for that window:
  volume bid | delta | ask; total with % of the average of equal windows (orange = more, turquoise = less); aggression
  (delta per second); bar time (blue = faster, red = slower than usual). Below, for the live bar: POC, POC diagonals
  (POC | sum | top), delta % (POC delta in % of average), POC-Vol (volume of the 3 levels around the POC).
- **Liquidity:** net change bid | delta | ask of the running bar, book of the levels visible in the DOM, a verdict which
  side is stacking more, and red "event possible" when both sides pull.
- **Replay:** `[-] Tempo [+]` (green = running, yellow = paused), Start (at the last visible bar – scroll there first),
  Pause/Resume, Stop. Tempo steps 0.5 to 200. The SC replay box is hidden.
- **VP:** volume profiles (section 7).
- **Journal (Tagebuch):** browse journal entries (section 7).
- **MTS:** market and bar type, compute-core status (bars calculated), liquidity source (live book / historical depth),
  settings files in use. Below: button **Settings**.

**Settings drawer** (button "Settings" / "Einstellungen")
- Built automatically from all MTS settings – every setting has a name, a range and a help text.
- Groups open/close with a click. Click a label = help text in the bottom line (with default and key).
- Numbers: `-` / `+` (Shift ×10, Ctrl ×100). Click the value to type it, Enter saves, Esc cancels.
- Choices: one button per option, or `<` option `>`. On/off: two buttons. Colours: click = Windows colour picker.
- Circular arrow on the right: reset to default (only visible when the value differs).
- Lock at the top: locked at start (`CK_SPERRE`), unlock before changing. `x` closes the drawer.
- Changes apply immediately (files are re-read within about 2 seconds).

**Which file holds a setting?** The help line says it. Three scopes: interface/colours for all charts (`Allgemein.txt`),
calculation and trade per market (`ES.txt` etc.), display per chart (`Chart_<Market>_<Bars>.txt`, e.g. a 14-range and
a 24-range chart can look different).

---

## 4. Price chart and indicators

Per bar (MTS Indikatoren and Numberbar):
- **Yellow dot = 1-bar VWAP**, the bar's centre of gravity.
- **Thin cross line = bar POC** (price with the highest volume in the bar; on a tie the level closer to the bar middle wins).
- **Red/blue box = POC delta:** its **height is the volume** at the POC (3 levels), its **width the delta** there. Blue =
  buyers dominate at the POC, red = sellers.
- **POC MA1/MA2/MA3:** weighted moving averages of the bar POC (defaults 5 / 8 / 15). The trend line is built from the
  volume-weighted POC, not from the close.

**MTS Region** (sub-window): SMI with average and overbought/oversold, **DeltaValue** (SC "Value Chart" calculation applied
to the cumulative delta, reset per trading day – drawn as candles), POC delta histogram, Vol3 histogram, POC score, SMI
cross score. Every line on/off per region ("Region 1: display"). Scaling: "Region 1: scaling" – axis ±value, auto height %,
per-group factor or auto normalisation. All lines are SC subgraphs, so they show in Tool Value and the data window.
A second sub-window: add MTS Region again and set Region No. = 2.

All standard SC studies can be used alongside MTS in any region.

---

## 5. Numberbar (footprint and liquidity in the chart)

**Footprint part** (`NB_FOOTPRINT` = number of bars side by side, the right one is the live bar, 0 = off; at most as many
as the compute core keeps, `FOOTPRINT_BARS`, max 20). `NB_SYNC_DOM` = show as many bars as the DOM footprint.
- Per price level **Bid | Ask**. The **diagonally dominant side is coloured**: bid red if Bid[P] > Ask[P+1]; ask blue if
  Ask[P] > Bid[P-1]. Intensity = cell volume / highest bid or ask in the bar.
- The bar's candle between the columns.
- **Diagonal delta** next to it: Ask[P] − Bid[P−1] per level as bar and number (blue = buyers lift, red = sellers hit).
- Frames: **white = POC**, **gold = absorption at the extreme**, **yellow with arrow = current price**.
- Header: delta (shaded relative to the largest of the last N bars) and volume (orange = more, turquoise = less than
  usual). Footer: aggression (delta/s) and bar duration (blue = faster, red = slower).
- "Usual" always means compared with the last `N_VERGLEICH` bars.

**Liquidity part** (`NB_LIQ`), per side from outside to the middle:
- **Book:** resting limit orders now.
- **Net change** – live calculation from the order book per level:
  `Net = book now − book at bar start + volume traded at the level`.
  Adding back what was traded separates real limit changes from fills: **+ = stacking** (new limits), **− = pulling**
  (limits removed). Colours by default: bid + / ask − blue, bid − / ask + red (optionally mirrored in down bars).
  **Grey frame** = the level changed sides during the bar; net counts only since then.
- **T/S:** volume traded since price last came to the level.
- **Orange frame** = absorption in the book. Red **"event possible"** = both sides are pulling.
- Bottom: net sums and the verdict which side is stacking more.
- Needs market depth (live or replay with recorded depth). Grey/empty = no depth data.

Purpose: read the running bar right at price – who is aggressive (delta, aggression, diagonals), where volume trades (POC),
where it is absorbed, and how limits react (net, book, side changes) – without looking at a separate DOM.

---

## 6. MTS DOM

Own chart DOM in SC column "General Purpose 1".
- **Layout "numberbar integrated"** (default): P/L | price | bid book | net | footprint bid | T/S | diagonal delta | T/S |
  footprint ask | net | ask book. The bid-side order from outside to inside is selectable (**"columns per side"**, 24
  layouts); the ask side mirrors it, delta stays in the middle. Standard layout: P/L | bid book | [net | T/S] | price |
  [T/S | net] | ask book.
- **Footprint** summed over the live bar and the N−1 bars before (`DOM_FP_BARS`, also `[-]/[+]` in the Bar tile; at most
  `FOOTPRINT_BARS`). POC frame white, absorption gold, diagonal delta as bars.
- **Liquidity** per level: net (same formula as section 5), T/S, grey frame = side change, orange frame = absorption in the
  book. Header right: "EVENT possible" or "hist. Depth" (historical depth data).
- Colours like the SC DOM: bid book blue, ask book red; the last print is framed across its side.
- Header: SIM/LIVE, quantity, attached, click mode, warnings (AUTO OFF, locked). Footer: last message.

**Click orders** (like SC "Chart DOM Settings >> Order Types", Last as reference):
- Bid half: above Last = Buy Stop, at/below Last = Buy Limit.
- Ask half: at/above Last = Sell Limit, below Last = Sell Stop.
- Click own order = select; then another row = move, same row again = delete; dragging moves it.
- Click mode on / Ctrl / off in "DOM: orders". Quantity, max position and live permission come from the Trade settings.
  Max position is checked before sending (position + open entry orders + quantity). Requires Auto Trading Enabled.

**Chart follows DOM** ("DOM: ladder & position", default off): sets the price axis so that **one ladder row = one price
level** in the chart – candles, footprint and DOM have the same height per tick. Works with fixed rows (numberbar "fixed
rows" or own fixed rows). Turning it off restores the previous axis.

---

## 7. VP (volume profile), channel and journal

### VP and channel (study MTS Kanal, tile "VP")
A **VP** is a range from a start bar to an end bar with its own **volume profile**.
- **Set VP:** click the button, then the start bar and the end bar (last bar = runs live). **End VP** (shown while the
  selected VP runs): click the end bar. Correct: drag the handles (small squares at the corners). A new VP ends a running
  one at the bar before. **Delete VP:** button, then click into the VP.
- `< VP 4/4 >` browse. **Channel:** regression channel for this VP on/off (default for new ones `KA_KANAL_NEU`).
  **Pin / always show:** this VP stays visible as a profile. **Type:** Auto (from the regression) / Up / Range / Down.
  **VP note:** click to write (Ctrl+Enter saves, Esc discards).
- In the chart: profile on the left side of the VP with POC (magenta), value area (VAH/VAL, `KA_PROF_VA` %), VWAP,
  info block (e.g. range, volume, POC volume), POC migration. The channel label shows type, R² and slope in ticks per bar,
  e.g. "Range R2 0.02 +0.1 T/Bar". The regression is always calculated (also for the journal export), shown only if on.
- View (`KA_ANSICHT`): selected and pinned VPs as profile, all others as a thin frame "VPn" (or all profile / all frame).
- Click a VP = select it. **While Ctrl is held, VP handles and clicks are off** (Ctrl belongs to the journal).

### Journal (study MTS Tagebuch, tile "Journal")
One function for everything. The **code** at the start of the text decides what an entry is.
| Action | How |
|---|---|
| Entry at one bar | **Ctrl+click** the bar → input window, type code + text, **Enter** |
| Entry over two bars | **Shift+Ctrl+click** bar 1, then **click** bar 2, type, Enter (bar 2 carries the info) |
| Trade in hindsight | two bars with **L** (long) or **S** (short): bar 1 = entry, bar 2 = exit, e.g. `L BO breakout from range` |
| Running trade | Ctrl+click only the entry bar with `L`/`S` – the line runs to the last bar, result live |
| End a running trade | Ctrl+click the exit bar → button **"Exit here"** |
| Not traded | `NT` at the bar (with a reason, e.g. `NT FBO against POC`) |
| Info | any other code (e.g. `FBO`, `Abs`, `HL`) |
| Edit | Ctrl+click bar 1 / bar 2 / the label of the entry, or click the header line in the tile |
| Delete | button Delete in the window, save an empty text, or "delete" in the tile |
| Cancel | Esc or Cancel |

- The **price** is where you click, rounded to the tick and limited to the bar's high/low – for trades click where you
  would have entered/exited.
- Input window keys: Enter = save, Tab = to the note, Ctrl+Enter in the note = save, Esc = cancel.
- **Entry mode** is stored automatically: **live** (bar 1 is the running or just closed bar while replay/live runs) or
  **hindsight**.
- **In the chart:** trade = dashed line from entry to exit (green long / red short) with the codes at the exit. **Click
  the label** to expand it: result in ticks and in $ for one contract (e.g. `L BO +24 T +300 $`) and a second line with
  the text. Click again or beside it to collapse. One-bar entries = a dot at the click price with the codes above or
  below the bar. Info over two bars = dashed white line. Labels sit on a dark field so candles don't hide them. Only codes
  from the code list (max three) are written in the chart; free text is in the tile.
- **Tile:** `< 7/9 >` browse (brings the entry into view), header line = codes + text (click to edit), times and prices,
  for trades the result, the largest run for/against (max +27 / −6) and number of bars, note field.
- **Code list** `Data/MTS/Kuerzel.txt` (format `code = explanation | group`), may be extended; case-insensitive.
  Built-in: structure HH, HL, LH, LL, TL (test low), TH (test high); movement BO (breakout), FBO (false breakout),
  Mom (momentum), RT (retracement); order flow Abs, Exh, Iceberg, Pull, Stack, Akk (accumulation), Dist (distribution);
  trading L, S, NT, Stop.
- **Export** (for your own evaluation): `Tagebuch_Werte_<Market>_<Bars>.csv` (one row per entry with result, MFE/MAE and
  the context at the reference bar: delta, POC delta, net, absorption, aggression, channel type/position, VP position
  relative to POC and value area, note) and `Tagebuch_Bars_<Market>_<Bars>.csv` (all bars of the entry with all
  compute-core values, plus a few bars before entry). Entries are found again by symbol, bar type and bar start time.
- Journal and VPs are stored **per market** (shared by all bar types of that market); two charts of the same market
  reload each other's changes within about 2 seconds.

---

## 8. Trade tile and safety

- **Account:** SIM (green) / LIVE (red), account name, "Auto on" or red "Auto OFF" / "Live locked".
- **Position** (long green / short red / flat) and net P/L (day + open); with a position also open ticks and amount.
- **Att on/off** switches "Use Attached Orders" in the SC trade window; `< template >` browses the SC templates (*.twconfig).
  "(0)" = attached on but the template has no orders.
- **Qty** −/+ (at most max position), also written to the SC trade window.
- **BUY / SELL:** MKT = market, ASK = limit at the ask, BID = limit at the bid. Grey = sending not possible.
- **Flatten** (close all + cancel orders), **Reverse** (flatten, then market in the other direction once flat), **Cancel**
  (cancel all orders), **BE** (move attached stops to entry + ticks, only if price is beyond it).
- Rules: an order larger than the opposite position is rejected (flatten or reverse first). Requires Auto Trading Enabled
  per chart. **Live accounts only with "allow live orders"** in the drawer group "Trade" (per market). Use SIM for testing.

---

## 9. Settings reference (generated from the settings files)

Every setting has a key. It is changed in the Cockpit drawer (Settings) or in the text file. Descriptions are currently in German (the interface is being translated) – translate them for the user. Format of a description: "Label: explanation (allowed values or range)". "Scope": all charts = Allgemein.txt, per market = <Market>.txt (e.g. ES.txt), per chart = Chart_<Market>_<Bars>.txt. The value shown is the developer's current value, which is usually the default.

### Cockpit: colours  (drawer group "Cockpit: Farben")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `CK_F_TEXT` | 230,230,230 | all charts | Text: Schriftfarbe. (Farbe R,G,B) |
| `CK_F_HINTERGRUND` | 28,28,36 | all charts | Hintergrund: Hintergrund der Kachelspalte. (Farbe R,G,B) |
| `CK_F_KOPF` | 55,55,80 | all charts | Kopf / Linien: Kachelkoepfe und Trennlinien. (Farbe R,G,B) |
| `CK_F_KNOPF` | 75,75,100 | all charts | Knopf: Knopf (nicht gewaehlt). (Farbe R,G,B) |
| `CK_F_AKTIV` | 40,80,220 | all charts | Knopf gewaehlt: Gewaehlte Option, aktiver Knopf. (Farbe R,G,B) |
| `CK_F_GEDIMMT` | 140,140,140 | all charts | Text blass: Bezeichnungen und Hinweise. (Farbe R,G,B) |
| `CK_F_LAEUFT` | 0,170,90 | all charts | Replay laeuft: Tempo-Feld, wenn das Replay laeuft. (Farbe R,G,B) |
| `CK_F_PAUSE` | 230,190,0 | all charts | Replay Pause: Tempo-Feld, wenn das Replay pausiert. (Farbe R,G,B) |
| `CK_F_BALKEN` | 50,50,62 | all charts | Feld leer: Grund der Wertfelder (schwache Werte). (Farbe R,G,B) |
| `CK_F_BUY` | 40,80,220 | all charts | Kauf / plus: Kauf, Ask, plus (Bar- und Liquiditaets-Kachel). (Farbe R,G,B) |
| `CK_F_SELL` | 200,50,50 | all charts | Verkauf / minus: Verkauf, Bid, minus (Bar- und Liquiditaets-Kachel). (Farbe R,G,B) |
| `CK_F_PD_PLUS` | 30,70,255 | all charts | POC-Delta plus: POC-Delta: Farbe plus. (Farbe R,G,B) |
| `CK_F_PD_MITTE` | 110,90,130 | all charts | POC-Delta Mitte: POC-Delta: Farbe um null. (Farbe R,G,B) |
| `CK_F_PD_MINUS` | 220,35,35 | all charts | POC-Delta minus: POC-Delta: Farbe minus. (Farbe R,G,B) |
| `CK_F_LONG` | 128,128,255 | all charts | Long / Gewinn: Long-Position, Gewinn. (Farbe R,G,B) |
| `CK_F_SHORT` | 200,60,60 | all charts | Short / Verlust: Short-Position, Verlust. (Farbe R,G,B) |

### Cockpit: position & size  (drawer group "Cockpit: Lage und Groesse")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `CK_LAGE` | 1 | all charts | Lage: Kachelspalte am rechten Fensterrand oder in der SC-Chart-DOM-Spalte 'General Purpose 2' rechts der Preisskala (Breite dort am Spaltenrand ziehen). Ist die Spalte nicht sichtbar, steht das Cockpit am Fensterrand. (0 = am rechten Fensterrand, 1 = in SC-Spalte General Purpose 2) |
| `CK_BREITE` | 230 | all charts | Breite: Breite der Kachelspalte am Fensterrand (Pixel). (120 .. 800) |
| `CK_OBEN` | 4 | all charts | Abstand oben: Abstand der Kachelspalte zur Oberkante des Preis-Charts (Pixel). (-200 .. 2000) |
| `CK_RECHTS` | 2 | all charts | Abstand rechts: Abstand zum rechten Fensterrand (nur Lage am Fensterrand, Pixel). (0 .. 2000) |
| `CK_INNENRAND` | 0 | all charts | Innenrand rechts: Innenrand rechts in der Spalte (Pixel), z. B. wenn SC rechts etwas ueberdeckt. (0 .. 400) |
| `CK_SCHRIFT` | 9 | all charts | Schriftgroesse: Schriftgroesse im Cockpit (Punkt). (6 .. 24) |
| `CK_ZEILE` | 39 | all charts | Zeilenhoehe: Zeilenhoehe im Cockpit (Pixel). (14 .. 60) |
| `CK_SPERRE` | 1 | all charts | Beim Start gesperrt: Einstellungen beim Start gesperrt: erst das Schloss oeffnen, dann aendern. (1 = an, 0 = aus) |
| `CK_SCHRIFT_GROSS` | 13 | all charts | Schriftgroesse gross: Grosse Werte in Signal und Trade (Uhr, Countdown, Richtung, Position, P/L) in Punkt. (6 .. 40) |
| `CK_REIHENFOLGE` | 18423169057 | all charts | Reihenfolge der Kacheln: Wird mit den kleinen Pfeilen in den Kachelkoepfen gesetzt (1 + je Kachel eine Ziffer: 0 MTS, 1 Replay, 2 Bar, 3 Liquiditaet, 4 Trade, 5 Auswertung, 6 VP, 7 Aufnahme, 8 Signal, 9 Box). (0 .. 99999999999) |

### Cockpit: replay  (drawer group "Cockpit: Replay")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `RP_TEMPO` | 1.0 | all charts | Tempo beim Start: Replay-Tempo beim Start; -/+ in der Kachel stellt es in Stufen (0.5 1 2 3 5 10 20 50 100 200) und merkt es sich. (0.5 .. 200.0) |
| `RP_BOX` | 1 | all charts | SC-Replay-Box ausblenden: SC oeffnet bei jedem Replay-Befehl seine Replay-Box; das Cockpit blendet sie ~3 s lang aus. (1 = an, 0 = aus) |
| `RP_CHARTS` | 0 | all charts | Charts: Replay nur dieses Chart, alle Charts des Chartbooks oder alle mit gleicher Link-Nummer. (0 = einzeln, 1 = alle, 2 = verlinkte) |

### Compute core: absorption  (drawer group "Rechenkern: Absorption")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `ZONE` | 3 | per market | Extrem-Zone: so viele Preisstufen am Hoch/Tief |
| `ABS_MULT` | 2 | per market | Absorption am Extrem: Volumen ab x-fachem ueblichem Volumen je Stufe |
| `ER_FENSTER` | 5 | per market | Effort vs Result: Fenster in Bars |
| `ER_NORMAL` | 55 | per market | Effort vs Result: Normalmass (uebliches Delta / Bewegung) ueber so viele Bars; auch fuer VolZ |
| `ER_MINDRUCK` | 2.00 | per market | Effort vs Result: Mindest-Delta-Druck (x normal), darunter ruhig |

### Compute core: footprint  (drawer group "Rechenkern: Footprint")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `IMB_FAKTOR` | 2.0 | per market | Imbalance diagonal ab diesem Faktor (Ask[P] >= Faktor x Bid[P-1]) |
| `IMB_MINVOL` | 100 | per market | Imbalance: Mindestvolumen der staerkeren Seite (Kontrakte) |

### Compute core: general  (drawer group "Rechenkern: allgemein")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `N_VERGLEICH` | 13 | per market | Vergleichsfenster in Bars fuer alle relativen Werte (Mittel, Max, Schattierung) |
| `FOOTPRINT_BARS` | 5 | per market | Stufen (Footprint) fuer so viele letzte Bars bereithalten (Numberbar, DOM) |
| `EXPORT` | 1 | per market | Merkmale je Bar in Data/MTS/Merkmale_<Symbol>_<Bar-Art>.csv schreiben (1 an, 0 aus) |

### Compute core: liquidity  (drawer group "Rechenkern: Liquiditaet")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `LIQ_ABSMIN` | 50 | per market | Absorption im Buch: mindestens so viel gehandelt (Kontrakte) und Seite nicht kleiner |
| `LIQ_URTEIL_AB` | 200 | per market | Urteil 'Seite stellt staerker nach' ab dieser Netto-Differenz (Kontrakte) |
| `LIQ_TSMIN` | 1 | per market | T/S: nur Trades ab dieser Groesse zaehlen |
| `LIQ_EVENT_PCT` | 40 | per market | Warnung 'Event moeglich', wenn beide Seiten unter x % ihres 5-min-Durchschnitts fallen (0 = aus) |
| `LIQ_EVENT_STUFEN` | 10 | per market | Event-Warnung: Buchtiefe ueber so viele Stufen je Seite |

### DOM: colours  (drawer group "DOM: Farben")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `DOM_F_ASK0` | 240,181,214 | all charts | Ask-Buch / minus 0 (hell): DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_ASK1` | 233,143,170 | all charts | Ask-Buch / minus 1: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_ASK2` | 255,91,91 | all charts | Ask-Buch / minus 2: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_ASK3` | 255,0,0 | all charts | Ask-Buch / minus 3: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_BID0` | 213,213,255 | all charts | Bid-Buch / plus 0 (hell): DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_BID1` | 165,170,239 | all charts | Bid-Buch / plus 1: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_BID2` | 131,128,251 | all charts | Bid-Buch / plus 2: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_BID3` | 35,78,203 | all charts | Bid-Buch / plus 3: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_TEXT` | 255,255,255 | all charts | Text: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_CUR` | 0,255,255 | all charts | Aktueller Preis: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_PRICE` | 170,170,170 | all charts | Preisspalte: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_BG` | 28,28,28 | all charts | Hintergrund: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_GRID` | 0,0,0 | all charts | Zeilenraster: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_HEAD` | 55,55,80 | all charts | Kopf / Linien: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_BUY` | 40,90,255 | all charts | Order Kauf: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_SELL` | 220,50,50 | all charts | Order Verkauf: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_STOP` | 255,140,0 | all charts | Order Stop: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_POS` | 0,200,90 | all charts | Einstand: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_ABS` | 0,0,0 | all charts | Rahmen Absorption: DOM-Farbe. (Farbe R,G,B) |
| `DOM_F_BGUP` | 0,0,0 | all charts | Hintergrund ueber Preis: DOM-Farbe. (Farbe R,G,B) |
| `DOM_COLORS` | 0 | per chart | Farben Schattierung: Eigene Farben unten oder die Stufen der MTS Numberbar (Seiten getauscht: Bid-Buch blau, Ask-Buch rot wie im SC-DOM). (0 = eigene, 1 = wie MTS Numberbar) |

### DOM: display  (drawer group "DOM: Anzeige")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `DOM_SHOW` | 0 | per chart | Anzeige: MTS DOM an oder aus. (0 = an, 1 = aus) |
| `DOM_VIEW` | 1 | per chart | Darstellung: Standard: Buch mit Preis in der Mitte (Liquiditaet zuschaltbar). Numberbar integriert: Footprint, Diagonal-Delta, Netto und T/S im DOM. (0 = Standard, 1 = Numberbar integriert) |
| `DOM_FP_BARS` | 1 | per chart | Footprint Bars: Footprint summiert ueber den Live-Bar und die N-1 Bars davor (hoechstens 'Footprint Bars' des Rechenkerns). (1 .. 50) |
| `DOM_SIDEORD` | 7 | per chart | Spalten je Seite: Numberbar integriert: Reihenfolge der Bid-Seite von aussen (links) nach innen zum Diagonal-Delta; die Ask-Seite ist gespiegelt. (0 = Buch / Netto / Footprint / T/S, 1 = Buch / Netto / T/S / Footprint, 2 = Buch / Footprint / Netto / T/S, 3 = Buch / Footprint / T/S / Netto, 4 = Buch / T/S / Netto / Footprint, 5 = Buch / T/S / Footprint / Netto, 6 = Netto / Buch / Footprint / T/S, 7 = Netto / Buch / T/S / Footprint, 8 = Netto / Footprint / Buch / T/S, 9 = Netto / Footprint / T/S / Buch, 10 = Netto / T/S / Buch / Footprint, 11 = Netto / T/S / Footprint / Buch, 12 = Footprint / Buch / Netto / T/S, 13 = Footprint / Buch / T/S / Netto, 14 = Footprint / Netto / Buch / T/S, 15 = Footprint / Netto / T/S / Buch, 16 = Footprint / T/S / Buch / Netto, 17 = Footprint / T/S / Netto / Buch, 18 = T/S / Buch / Netto / Footprint, 19 = T/S / Buch / Footprint / Netto, 20 = T/S / Netto / Buch / Footprint, 21 = T/S / Netto / Footprint / Buch, 22 = T/S / Footprint / Buch / Netto, 23 = T/S / Footprint / Netto / Bu |
| `DOM_LIQ` | 1 | per chart | Liquiditaet (Standard): Standard-Darstellung: Netto und T/S je Seite zwischen Buch und Preis (integriert immer an). (0 = aus, 1 = an) |
| `DOM_LIQLEV` | 0 | per chart | Liquiditaet: Level: Alle DOM-Level: Netto auch ausserhalb des Bars = Buch jetzt - Buch bei Bar-Beginn (nur mit Live-Buch). (0 = Stufen des Bars, 1 = alle DOM-Level) |
| `DOM_PRICECOL` | 2 | per chart | Preisspalte: Automatisch: aus, wenn die Zeilen an der Preisskala liegen (SC-Skala daneben). (0 = automatisch, 1 = an, 2 = aus) |
| `DOM_FONT` | 8 | per chart | Schriftgroesse: Schriftgroesse im DOM (Punkt). (5 .. 30) |
| `DOM_POCW` | 3 | per chart | POC-Rahmen: Staerke des POC-Rahmens im Footprint (Pixel). (1 .. 4) |

### DOM: ladder & position  (drawer group "DOM: Leiter und Lage")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `DOM_LADDER` | 1 | per chart | Leiter: Zeilen wie die MTS Numberbar (gleiche Zeilen und Zoom) oder eigene feste Zeilenhoehe bzw. an der Preisskala. (0 = wie MTS Numberbar, 1 = eigene feste Zeilen, 2 = eigene an Preisskala) |
| `DOM_CHART_FOLGT` | 0 | per chart | Chart folgt DOM: Preisachse des Charts so setzen, dass 1 Leiter-Zeile = 1 Preisstufe im Chart ist (Kerzen und DOM/Numberbar gleich hoch). Nur bei festen Zeilen; am besten mit Bezug Schlusskurs oder Mitte High/Low. aus = bisherige Achsen-Einstellung zurueck. (1 = an, 0 = aus) |
| `DOM_ROWH` | 30 | per chart | Zeilenhoehe: Eigene Leiter: Zeilenhoehe (Pixel). (6 .. 60) |
| `DOM_ANCHOR` | 3 | per chart | Bezug: Eigene feste Zeilen: Schlusskurs an der Preisskala, Preis immer mittig, Mitte mit Neu-Zentrieren ab N Ticks, oder Mitte High/Low des letzten Bars an der Preisskala (wie Numberbar). (0 = Schlusskurs, 1 = Mitte, Preis mittig, 2 = Mitte, neu zentrieren, 3 = Mitte High/Low) |
| `DOM_RECENTER` | 8 | per chart | Neu zentrieren ab: Eigene Leiter: neu zentrieren ab so vielen Ticks von der Mitte. (1 .. 500) |
| `DOM_POSMODE` | 2 | per chart | Lage: In der SC-Spalte General Purpose 1 rechts der Preisskala (dort enden die SC-Order-Linien; Breite oben am Spaltenrand ziehen, die Spalten verteilen sich darauf). Sonst fest am rechten Rand oder rechts neben der MTS Numberbar. (0 = fest am rechten Rand, 1 = neben MTS Numberbar, 2 = SC-Spalte General Purpose 1) |
| `DOM_XOFF` | 10 | per chart | Abstand: Abstand zum rechten Rand bzw. zur Numberbar (Pixel). (-2000 .. 2000) |

### DOM: orders  (drawer group "DOM: Orders")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `DOM_CLICK` | 0 | per chart | Klick-Orders: Bid-Haelfte unter/am Last = Buy Limit, darueber Buy Stop; Ask-Haelfte ueber/am Last = Sell Limit, darunter Sell Stop. Eigene Order anklicken = waehlen, dann Zeile = verschieben, nochmal = loeschen. (0 = an, 1 = nur mit Strg, 2 = aus) |

### DOM: widths  (drawer group "DOM: Breiten")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `DOM_PLW` | 0 | per chart | P/L: Breite der P/L-Spalte (Pixel, 0 = aus). (0 .. 300) |
| `DOM_BOOKW` | 60 | per chart | Buch: Breite Bid- / Ask-Buch (Pixel). (20 .. 300) |
| `DOM_NETW` | 80 | per chart | Netto: Breite Netto je Seite (Pixel). (20 .. 300) |
| `DOM_TSW` | 50 | per chart | T/S: Breite T/S je Seite (Pixel). (16 .. 300) |
| `DOM_NBW` | 70 | per chart | Footprint: Numberbar integriert: Breite Footprint je Seite (Pixel). (20 .. 300) |
| `DOM_DIAGW` | 100 | per chart | Diagonal-Delta: Numberbar integriert: Breite Diagonal-Delta (Pixel). (20 .. 300) |

### Indicators chart: colours  (drawer group "Indikatoren Chart: Farben")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `IN_F_BARPOC` | 200,200,200 | all charts | Bar-POC Farbe: Linienfarbe. (Farbe R,G,B) |
| `IN_F_MA1` | 200,60,40 | all charts | POC MA1 Farbe: Linienfarbe. (Farbe R,G,B) |
| `IN_F_MA2` | 0,150,0 | all charts | POC MA2 Farbe: Linienfarbe. (Farbe R,G,B) |
| `IN_F_MA3` | 0,230,230 | all charts | POC MA3 Farbe: Linienfarbe. (Farbe R,G,B) |
| `IN_W_BARPOC` | 4 | per chart | Bar-POC Breite: Linienstaerke (Pixel). (1 .. 10) |
| `IN_W_MA1` | 1 | per chart | POC MA1 Breite: Linienstaerke (Pixel). (1 .. 10) |
| `IN_W_MA2` | 1 | per chart | POC MA2 Breite: Linienstaerke (Pixel). (1 .. 10) |
| `IN_W_MA3` | 3 | per chart | POC MA3 Breite: Linienstaerke (Pixel). (1 .. 10) |

### Indicators chart: display  (drawer group "Indikatoren Chart: Anzeige")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `IN_BARPOC` | 1 | per chart | Bar-POC: Bar-POC im Preis-Chart anzeigen (Werte aus dem Rechenkern). (1 = an, 0 = aus) |
| `IN_MA1` | 1 | per chart | POC MA1: POC MA1 im Preis-Chart anzeigen (Werte aus dem Rechenkern). (1 = an, 0 = aus) |
| `IN_MA2` | 1 | per chart | POC MA2: POC MA2 im Preis-Chart anzeigen (Werte aus dem Rechenkern). (1 = an, 0 = aus) |
| `IN_MA3` | 1 | per chart | POC MA3: POC MA3 im Preis-Chart anzeigen (Werte aus dem Rechenkern). (1 = an, 0 = aus) |
| `IN_PDRECT` | 1 | per chart | POC-Delta Rechtecke: Rechteck am POC jedes Bars: Breite und Farbe = POC-Delta, Hoehe = Volumen der 3 Stufen (relativ zu den N Bars des Rechenkerns). (1 = an, 0 = aus) |
| `IN_PD_MAXH` | 6.0 | per chart | Rechteck max. Hoehe: POC-Delta-Rechteck: groesste Hoehe in Ticks. (1.0 .. 50.0) |
| `IN_PD_MINW` | 2 | per chart | Rechteck min. Breite: POC-Delta-Rechteck: Mindestbreite in Pixel. (1 .. 50) |

### Indicators: DeltaValue  (drawer group "Indikatoren: DeltaValue")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `DV_LAENGE` | 8 | per market | Value-Chart Laenge: DeltaValue: Laenge der Value-Chart-Rechnung (wie SC 'Value Chart', Length). (2 .. 10000) |
| `DV_RESET` | 2 | per market | Kum. Delta Reset: Kumuliertes Delta beginnt neu am Start des Handelstags, an beiden Session-Starts oder nie (wie SC 'Cumulative Delta Bars - Volume'). (0 = Handelstag, 1 = beide Sessions, 2 = nie) |

### Indicators: SMI  (drawer group "Indikatoren: SMI")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `SMI_OB` | 40 | per market | Overbought: SMI Overbought-Linie. (-100 .. 100) |
| `SMI_OS` | -40 | per market | Oversold: SMI Oversold-Linie. (-100 .. 100) |
| `SMI_D` | 4 | per market | %D Laenge: SMI %D Length (EMA-Glaettung). (1 .. 200) |
| `SMI_K` | 5 | per market | %K Laenge: SMI %K Length (Hoch/Tief-Fenster). (1 .. 200) |
| `SMI_EMA` | 3 | per market | Average Laenge: SMI Average: EMA-Laenge. (1 .. 200) |
| `SMI_QUELLE` | 0 | per market | Preisquelle: Welcher Preis mit der Mitte der Hoch/Tief-Spanne verglichen wird. Close = wie das SC-Original. (0 = Bar-POC, 1 = Close, 2 = HLC/3, 3 = OHLC/4) |
| `SMI_ABSTAND` | 10.0 | per market | Kreuz: Abstand voll: Kreuz-Score: Abstand SMI - Average fuer volle Punkte. (0.1 .. 200.0) |
| `SMI_ZONE` | 10 | per market | Kreuz: Zone wirkt: Kreuz-Score: so viele Bars wirkt die Zone (unter Oversold / ueber Overbought) nach. (1 .. 200) |
| `SMI_MITTE` | 30 | per market | Kreuz: Anteil Mitte %: Kreuz-Score: Anteil ohne Zone bzw. in der Mitte. (1 .. 100) |
| `SMI_RANGE` | 20 | per market | Range nach Bars: Range: SMI so viele Bars nicht ueber Overbought/Oversold. (1 .. 500) |

### Indicators: bar POC  (drawer group "Indikatoren: Bar-POC")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `POC_MA1` | 5 | per market | MA1 Laenge: Bar-POC MA1: Laenge des gewichteten gleitenden Durchschnitts (WMA). (1 .. 200) |
| `POC_MA2` | 8 | per market | MA2 Laenge: Bar-POC MA2: Laenge (WMA). (1 .. 200) |
| `POC_MA3` | 18 | per market | MA3 Laenge: Bar-POC MA3: Laenge (WMA). (1 .. 500) |
| `POC_RICHTUNG` | 1 | per market | Richtung: 1: Bar-POC ueber/unter MA1 und MA2. 2: dazu MA1 ueber/unter MA2. 3: MA1 und MA2 ueber/unter MA3. Bei 1 und 2 bleibt dazwischen die letzte Richtung. (0 = POC kreuzt MA1+MA2, 1 = + MA1 kreuzt MA2, 2 = MA1/MA2 kreuzt MA3) |
| `POC_MIG_BARS` | 3 | per market | Migration Fenster: POC-Migration: Wanderung des Bar-POC in Ticks je Bar ueber so viele Bars. (1 .. 100) |
| `POC_MIG_VOLL` | 10.0 | per market | Migration voll ab: Score: Migration in Richtung ab so vielen Ticks je Bar = volle Punkte. (0.1 .. 50.0) |
| `POC_LAGE_ANTEIL` | 30 | per market | Lage voll bis %: Score: POC-Lage im Bar bis zu diesem Anteil (von der guenstigen Seite) = volle Punkte. (5 .. 95) |
| `POC_LAGE_SEITE` | 0 | per market | Lage bestaetigt Long: Welche POC-Lage im Bar eine Long-Richtung bestaetigt. (0 = POC unten = Long, 1 = POC oben = Long) |

### Journal  (drawer group "Tagebuch")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `TB_COL_LONG` | 0,200,90 | all charts | Farbe Long-Trade: Linie und Kuerzel von Long-Trades (L). (Farbe R,G,B) |
| `TB_COL_SHORT` | 230,60,60 | all charts | Farbe Short-Trade: Linie und Kuerzel von Short-Trades (S). (Farbe R,G,B) |
| `TB_COL_INFO` | 220,220,220 | all charts | Farbe Info: Linie und Kuerzel aller anderen Eintraege (NT, FBO, Abs ...). (Farbe R,G,B) |
| `TB_COL_SEL` | 255,210,0 | all charts | Farbe gewaehlt: Der in der Kachel gewaehlte Eintrag. (Farbe R,G,B) |
| `TB_ZEIGEN` | 1 | per chart | Eintraege im Chart: Linien und Kuerzel der Tagebuch-Eintraege im Chart zeigen (aus = nur die Kachel). (1 = an, 0 = aus) |
| `TB_ERGEBNIS` | 1 | per chart | Ergebnis an Trades: An Trade-Linien (L / S) das Ergebnis in Ticks hinter das Kuerzel schreiben. (1 = an, 0 = aus) |
| `TB_TEXT` | 9 | per chart | Schrift: Schriftgroesse der Kuerzel im Chart (Punkt). (5 .. 30) |
| `TB_NOTIZ` | 1 | per chart | Text im Chart: Zweite Zeile am Kuerzel mit Freitext und Notiz (gekuerzt): nie, nur beim gewaehlten Eintrag oder bei allen. (0 = nur Kuerzel, 1 = gewaehlter mit Text, 2 = alle mit Text) |

### Numberbar: display  (drawer group "Numberbar: Anzeige")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `NB_FOOTPRINT` | 1 | per chart | Footprint Bars: So viele Bars nebeneinander (der letzte ist der Live-Bar); 0 = aus (nur Liquiditaet). Hoechstens so viele, wie der Rechenkern bereithaelt (Rechenkern: Footprint Bars). (0 .. 20) |
| `NB_SYNC_DOM` | 0 | per chart | Bars wie DOM: An: Footprint zeigt so viele Bars wie der DOM-Footprint summiert (DOM: Footprint Bars, auch [-]/[+] in der Cockpit-Kachel Bar), hoechstens 20. 'Footprint Bars' = 0 schaltet trotzdem aus. (1 = an, 0 = aus) |
| `NB_LIQ` | 1 | per chart | Liquiditaet: Liquiditaets-Spalte (Buch, Netto, T/S) rechts neben dem Footprint. (1 = an, 0 = aus) |
| `NB_GROESSE` | 1 | per chart | Groesse: Zeilen an der Preisachse oder mit fester Zeilenhoehe (dann mit eigener Preisspalte). (0 = an Preisskala, 1 = feste Zeilen) |
| `NB_ZEILE` | 30 | per chart | Zeilenhoehe: Feste Zeilenhoehe (Pixel). (6 .. 60) |
| `NB_BEZUG` | 1 | per chart | Bezug: Feste Zeilen: welcher Preis auf seiner Hoehe an der Preisskala bleibt (oder die Mitte des Chartbereichs). (0 = Schlusskurs, 1 = Mitte High/Low, 2 = Mitte Chart) |
| `NB_PREISSPALTE` | 0 | per chart | Preisspalte: Feste Zeilen: eigene Preisspalte links (die Zeilen passen dann nicht mehr zur Preisachse). (1 = an, 0 = aus) |
| `NB_POSITION` | 0 | per chart | Position: Neben dem letzten Bar oder fest am rechten Rand des Preis-Charts. (0 = neben letztem Bar, 1 = am rechten Rand) |
| `NB_ABSTAND` | 50 | per chart | Abstand: Abstand zum letzten Bar bzw. zum rechten Rand (Pixel). (-200 .. 1000) |
| `NB_BREITE_BA` | 140 | per chart | Breite Bid/Ask: Breite der Spalte Bid / Ask (Pixel). (30 .. 400) |
| `NB_BREITE_DIFF` | 100 | per chart | Breite Differenz: Breite der Spalte diagonale Differenz (Pixel). (20 .. 400) |
| `NB_KERZE` | 25 | per chart | Kerzenbreite: Breite der Kerze zwischen den Spalten (Pixel). (2 .. 60) |
| `NB_SCHRIFT` | 8 | per chart | Schriftgroesse: Schriftgroesse der Zahlen (Punkt). (5 .. 30) |
| `NB_RAHMEN` | 4 | per chart | Rahmenstaerke: Staerke der Rahmen (Bar, POC, Absorption, Preis) in Pixel. (1 .. 10) |

### Numberbar: footprint colours  (drawer group "Numberbar: Farben Footprint")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `NB_F_ASK0` | 213,213,255 | all charts | Ask Stufe 0 (hell): Ask-Seite / plus, hellste Stufe. (Farbe R,G,B) |
| `NB_F_ASK1` | 170,170,255 | all charts | Ask Stufe 1: Ask-Seite / plus. (Farbe R,G,B) |
| `NB_F_ASK2` | 64,64,255 | all charts | Ask Stufe 2: Ask-Seite / plus. (Farbe R,G,B) |
| `NB_F_ASK3` | 0,0,159 | all charts | Ask Stufe 3 (kraeftig): Ask-Seite / plus, kraeftigste Stufe. (Farbe R,G,B) |
| `NB_F_BID0` | 255,213,213 | all charts | Bid Stufe 0 (hell): Bid-Seite / minus, hellste Stufe. (Farbe R,G,B) |
| `NB_F_BID1` | 255,170,170 | all charts | Bid Stufe 1: Bid-Seite / minus. (Farbe R,G,B) |
| `NB_F_BID2` | 255,64,64 | all charts | Bid Stufe 2: Bid-Seite / minus. (Farbe R,G,B) |
| `NB_F_BID3` | 181,0,0 | all charts | Bid Stufe 3 (kraeftig): Bid-Seite / minus, kraeftigste Stufe. (Farbe R,G,B) |
| `NB_F_TEXT` | 255,255,255 | all charts | Text: Schrift auf dunklem Grund (auf hellem Grund schwarz). (Farbe R,G,B) |
| `NB_F_RAHMEN` | 100,100,255 | all charts | Rahmen Bar: Rahmen um die Bid/Ask-Spalte. (Farbe R,G,B) |
| `NB_F_POC` | 255,255,255 | all charts | Rahmen POC: Rahmen um die POC-Stufe. (Farbe R,G,B) |
| `NB_F_ABS` | 255,215,0 | all charts | Rahmen Absorption: Rahmen um die Stufe mit Absorption am Extrem. (Farbe R,G,B) |
| `NB_F_PREIS` | 255,255,0 | all charts | Aktueller Preis: Rahmen und Pfeil am aktuellen Preis. (Farbe R,G,B) |
| `NB_F_SCHNELL` | 40,90,255 | all charts | Dauer schneller: Bar-Dauer kuerzer als ueblich. (Farbe R,G,B) |
| `NB_F_LANGSAM` | 230,40,40 | all charts | Dauer langsamer: Bar-Dauer laenger als ueblich. (Farbe R,G,B) |
| `NB_F_VOLHOCH` | 255,140,0 | all charts | Volumen hoeher: Volumen hoeher als ueblich. (Farbe R,G,B) |
| `NB_F_VOLNIEDRIG` | 0,130,170 | all charts | Volumen niedriger: Volumen niedriger als ueblich. (Farbe R,G,B) |
| `NB_F_KERZEAUF` | 80,140,255 | all charts | Kerze aufwaerts: Kerze aufwaerts. (Farbe R,G,B) |
| `NB_F_KERZEAB` | 255,70,70 | all charts | Kerze abwaerts: Kerze abwaerts. (Farbe R,G,B) |
| `NB_F_PREISSPALTE` | 170,170,170 | all charts | Preisspalte: Zahlen der Preisspalte. (Farbe R,G,B) |

### Numberbar: liquidity  (drawer group "Numberbar: Liquiditaet")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `LQ_ABSTAND` | 10 | per chart | Abstand: Abstand der Liquiditaets-Spalte zum Footprint (Pixel). (-200 .. 500) |
| `LQ_BREITE_NETTO` | 70 | per chart | Breite Netto: Breite Netto je Seite (Pixel). (10 .. 500) |
| `LQ_BREITE_TS` | 70 | per chart | Breite T/S: Breite T/S je Seite (Pixel). (10 .. 500) |
| `LQ_BUCH` | 1 | per chart | Buch: Aktuelle Limits im Buch aussen anzeigen (nur auf der Seite, auf der die Stufe jetzt liegt). (1 = an, 0 = aus) |
| `LQ_ZAHLEN` | 1 | per chart | Zahlen: Zahlen in der Liquiditaets-Spalte anzeigen. (1 = an, 0 = aus) |
| `LQ_FARBE` | 0 | per chart | Farbe Netto: Standard: Bid + und Ask - blau, Bid - und Ask + rot. Up/Downtrend: im Upbar wie Standard, im Downbar gespiegelt. (0 = Standard, 1 = Up/Downtrend) |

### Numberbar: liquidity colours  (drawer group "Numberbar: Farben Liquiditaet")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `LQ_F_BID` | 226,75,74 | all charts | T/S Bid: In den Bid verkauft (T/S). (Farbe R,G,B) |
| `LQ_F_ASK` | 55,138,221 | all charts | T/S Ask: In den Ask gekauft (T/S). (Farbe R,G,B) |
| `LQ_F_ABS` | 239,159,39 | all charts | Rahmen Absorption: Absorption im Buch: viel gehandelt, Seite nicht kleiner. (Farbe R,G,B) |
| `LQ_F_TEXT` | 230,230,230 | all charts | Text: Schrift. (Farbe R,G,B) |
| `LQ_F_LINIE` | 110,110,110 | all charts | Linie / Buch: Mittellinie, Buch-Zahlen, Rahmen Seitenwechsel. (Farbe R,G,B) |
| `LQ_F_PREIS` | 255,255,0 | all charts | Aktueller Preis: Rahmen am aktuellen Preis. (Farbe R,G,B) |

### Region 1: display  (drawer group "Region 1: Anzeige")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `RG1_SMI` | 1 | per chart | SMI: SMI in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_SMIAVG` | 1 | per chart | SMI Average: SMI Average in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_OB` | 1 | per chart | SMI Overbought: SMI Overbought in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_OS` | 1 | per chart | SMI Oversold: SMI Oversold in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_DVK` | 1 | per chart | DeltaValue Kerzen: DeltaValue Kerzen in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_DVLINE` | 0 | per chart | DeltaValue: DeltaValue in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_DVSTUFE` | 0 | per chart | DeltaValue Stufen +-: DeltaValue Stufen +- in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_PD` | 1 | per chart | POC-Delta: POC-Delta in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_VOL3` | 1 | per chart | Vol3 (POC +-1): Vol3 (POC +-1) in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_SMISC` | 0 | per chart | SMI Kreuz-Score: SMI Kreuz-Score in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_POCSC` | 0 | per chart | POC-Score: POC-Score in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |
| `RG1_NULL` | 1 | per chart | Nulllinie: Nulllinie in Region 1 anzeigen (MTS Region mit Region-Nr 1). Skala ueber den Scale-Button der Study. (1 = an, 0 = aus) |

### Region 1: scaling  (drawer group "Region 1: Skalierung")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `RG1_SKALA` | 100.0 | per chart | Skala +-: Nur die Achse: symmetrisch von -Wert bis +Wert (setzt die Study selbst auf User Defined). Die Linien aendern sich dadurch nicht - bei auto fuellen sie 'Auto Hoehe %' der Achse, sonst gilt der Faktor. 0 = Einstellung im SC-Scale-Dialog. (0.0 .. 1000000.0) |
| `RG1_AUTO_HOEHE` | 100 | per chart | Auto Hoehe %: auto: so viel % der Achse (Skala +-) fuellt der groesste Betrag. Groesser = Linien hoeher. Bei auto aendert die Skala selbst das Bild nicht, nur die Achsenzahlen. (5 .. 500) |
| `RG1_AUTO_BARS` | 20 | per chart | Auto ueber Bars: auto: der groesste Betrag der letzten so vielen Bars (am rechten Rand) bestimmt den gemeinsamen Faktor aller Bars. (10 .. 100000) |
| `RG1_AUTO_SMI` | 1 | per chart | SMI (mit Average, OB/OS): auto: Automatisch normieren: ein Faktor fuer alle Bars - der groesste Betrag der letzten 'Auto ueber Bars' liegt bei 'Auto Hoehe %' der Achse (Skala +-). aus = fester Faktor % (Zeile darunter). (1 = an, 0 = aus) |
| `RG1_FAK_SMI` | 60.00 | per chart | SMI (mit Average, OB/OS): Faktor %: Faktor in % (100 = Originalwert), nur bei auto aus. -/+ mit Shift x10, Strg x100, Wert anklicken = eintippen. Originalwerte stehen unsichtbar im Tool Value / Datenfenster. (0.01 .. 100000.00) |
| `RG1_AUTO_DV` | 1 | per chart | DeltaValue (Kerzen, Linie, Stufen): auto: Automatisch normieren: ein Faktor fuer alle Bars - der groesste Betrag der letzten 'Auto ueber Bars' liegt bei 'Auto Hoehe %' der Achse (Skala +-). aus = fester Faktor % (Zeile darunter). (1 = an, 0 = aus) |
| `RG1_FAK_DV` | 100.00 | per chart | DeltaValue (Kerzen, Linie, Stufen): Faktor %: Faktor in % (100 = Originalwert), nur bei auto aus. -/+ mit Shift x10, Strg x100, Wert anklicken = eintippen. Originalwerte stehen unsichtbar im Tool Value / Datenfenster. (0.01 .. 100000.00) |
| `RG1_AUTO_PD` | 1 | per chart | POC-Delta: auto: Automatisch normieren: ein Faktor fuer alle Bars - der groesste Betrag der letzten 'Auto ueber Bars' liegt bei 'Auto Hoehe %' der Achse (Skala +-). aus = fester Faktor % (Zeile darunter). (1 = an, 0 = aus) |
| `RG1_FAK_PD` | 40.00 | per chart | POC-Delta: Faktor %: Faktor in % (100 = Originalwert), nur bei auto aus. -/+ mit Shift x10, Strg x100, Wert anklicken = eintippen. Originalwerte stehen unsichtbar im Tool Value / Datenfenster. (0.01 .. 100000.00) |
| `RG1_AUTO_VOL3` | 1 | per chart | Vol3: auto: Automatisch normieren: ein Faktor fuer alle Bars - der groesste Betrag der letzten 'Auto ueber Bars' liegt bei 'Auto Hoehe %' der Achse (Skala +-). aus = fester Faktor % (Zeile darunter). (1 = an, 0 = aus) |
| `RG1_FAK_VOL3` | 1.00 | per chart | Vol3: Faktor %: Faktor in % (100 = Originalwert), nur bei auto aus. -/+ mit Shift x10, Strg x100, Wert anklicken = eintippen. Originalwerte stehen unsichtbar im Tool Value / Datenfenster. (0.01 .. 100000.00) |
| `RG1_AUTO_SMISC` | 1 | per chart | SMI Kreuz-Score: auto: Automatisch normieren: ein Faktor fuer alle Bars - der groesste Betrag der letzten 'Auto ueber Bars' liegt bei 'Auto Hoehe %' der Achse (Skala +-). aus = fester Faktor % (Zeile darunter). (1 = an, 0 = aus) |
| `RG1_FAK_SMISC` | 100.00 | per chart | SMI Kreuz-Score: Faktor %: Faktor in % (100 = Originalwert), nur bei auto aus. -/+ mit Shift x10, Strg x100, Wert anklicken = eintippen. Originalwerte stehen unsichtbar im Tool Value / Datenfenster. (0.01 .. 100000.00) |
| `RG1_AUTO_POCSC` | 1 | per chart | POC-Score: auto: Automatisch normieren: ein Faktor fuer alle Bars - der groesste Betrag der letzten 'Auto ueber Bars' liegt bei 'Auto Hoehe %' der Achse (Skala +-). aus = fester Faktor % (Zeile darunter). (1 = an, 0 = aus) |
| `RG1_FAK_POCSC` | 100.00 | per chart | POC-Score: Faktor %: Faktor in % (100 = Originalwert), nur bei auto aus. -/+ mit Shift x10, Strg x100, Wert anklicken = eintippen. Originalwerte stehen unsichtbar im Tool Value / Datenfenster. (0.01 .. 100000.00) |

### Region: colours & levels  (drawer group "Region: Farben und Stufen")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `RG_F_SMI` | 0,255,0 | all charts | SMI Farbe: Linienfarbe. (Farbe R,G,B) |
| `RG_F_SMIAVG` | 255,0,255 | all charts | SMI Average Farbe: Linienfarbe. (Farbe R,G,B) |
| `RG_F_OB` | 255,255,0 | all charts | SMI Overbought Farbe: Linienfarbe. (Farbe R,G,B) |
| `RG_F_OS` | 255,127,0 | all charts | SMI Oversold Farbe: Linienfarbe. (Farbe R,G,B) |
| `RG_F_DVLINE` | 0,200,255 | all charts | DeltaValue Farbe: Linienfarbe. (Farbe R,G,B) |
| `RG_F_PD` | 128,128,255 | all charts | POC-Delta Farbe: POC-Delta: Grundfarbe (die Balken sind wie die Rechtecke eingefaerbt). (Farbe R,G,B) |
| `RG_F_VOL3` | 192,192,192 | all charts | Vol3 (POC +-1) Farbe: Linienfarbe. (Farbe R,G,B) |
| `RG_F_SMISC` | 255,200,0 | all charts | SMI Kreuz-Score Farbe: Linienfarbe. (Farbe R,G,B) |
| `RG_F_POCSC` | 200,200,200 | all charts | POC-Score Farbe: Linienfarbe. (Farbe R,G,B) |
| `RG_F_NULL` | 90,90,90 | all charts | Nulllinie Farbe: Linienfarbe. (Farbe R,G,B) |
| `RG_F_DVAUF` | 0,0,255 | all charts | DeltaValue Kerze auf: DeltaValue-Kerze, Close ueber Open. (Farbe R,G,B) |
| `RG_F_DVAB` | 220,50,50 | all charts | DeltaValue Kerze ab: DeltaValue-Kerze, Close unter Open. (Farbe R,G,B) |
| `RG_F_DVSTUFE` | 150,150,150 | all charts | DeltaValue Stufen Farbe: Linien DeltaValue Stufe + und -. (Farbe R,G,B) |
| `RG_F_DVRAHMEN` | 255,255,255 | all charts | DeltaValue Docht/Rahmen: Farbe von Docht und Rahmen der DeltaValue-Kerzen (1 Pixel). (Farbe R,G,B) |
| `RG_DVSTUFE` | 13 | per chart | DeltaValue Stufe: Hoehe der Linien DeltaValue Stufe + und - (z. B. 8 = ueberkauft/ueberverkauft). (1 .. 100) |
| `RG_W_SMI` | 1 | per chart | SMI Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_W_SMIAVG` | 1 | per chart | SMI Average Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_W_OB` | 1 | per chart | SMI Overbought Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_W_OS` | 1 | per chart | SMI Oversold Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_W_DVLINE` | 1 | per chart | DeltaValue Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_W_PD` | 10 | per chart | POC-Delta Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_W_VOL3` | 2 | per chart | Vol3 (POC +-1) Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_W_SMISC` | 1 | per chart | SMI Kreuz-Score Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_W_POCSC` | 1 | per chart | POC-Score Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_W_NULL` | 1 | per chart | Nulllinie Breite: Linien- bzw. Balkenstaerke (Pixel). (1 .. 10) |
| `RG_DVK_BREITE` | 60 | per chart | DeltaValue Kerze Breite %: Koerperbreite der DeltaValue-Kerzen in % des Bar-Abstands (waechst mit dem Zoom). 0 = Bar-Abstand minus 'Kerze Abstand'. (0 .. 100) |
| `RG_DVK_LUECKE` | 3 | per chart | DeltaValue Kerze Abstand: Nur bei Breite 0: Abstand zwischen den DeltaValue-Kerzen (Pixel); die Breite folgt dem Bar-Abstand des Charts. (0 .. 20) |

### Trade  (drawer group "Trade")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `TR_MENGE` | 1 | per market | Menge: Kontrakte je Order (Cockpit-Knoepfe und Klick-Orders im DOM); wird ins SC-Tradefenster uebernommen. (1 .. 1000) |
| `TR_MAX` | 5 | per market | Max. Position: Hoechste Position in einer Richtung inkl. offener Einstiegs-Orders; Menge hoechstens so gross. (1 .. 1000) |
| `TR_LIVE` | 1 | per market | Live-Orders erlauben: Aus: Orders nur auf Sim-Konten bzw. im Trade Simulation Mode. An: auch auf Live-Konten. (0 = aus, 1 = an) |
| `TR_BE` | 0 | per market | BE = Einstand + Ticks: BE setzt die Attached-Stopps auf den Einstand plus so viele Ticks. (-100 .. 100) |

### VP / channel: calculation  (drawer group "Kanal: Rechnung")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `KA_COL_GRIFF` | 255,210,0 | all charts | Griff Farbe: Griffe am Anfang und Ende des gewaehlten Kanals (ziehen = Rand verschieben). (Farbe R,G,B) |
| `KA_SIGMA` | 2.0 | per market | Kanalbreite x Sigma: Raender = Mitte +- k Sigma (Streuung der Closes um die Regressionsgerade). (0.5 .. 5.0) |
| `KA_FLATSLOPE` | 0.5 | per market | Range: Steigung unter: Range, wenn die Steigung unter so vielen Ticks je Bar liegt (oder R2 zu klein). (0.0 .. 20.0) |
| `KA_MINR2` | 0.30 | per market | Range: R2 unter: Range, wenn R2 der Regression darunter liegt. (0.00 .. 1.00) |
| `KA_BREAKBARS` | 3 | per market | Ausbruch nach Closes: Automatisches Ende nach so vielen Closes ausserhalb (nur live laufende Kanaele). (1 .. 20) |
| `KA_BREAKTICKS` | 8 | per market | Ausbruch ab Ticks: Close zaehlt erst so viele Ticks ausserhalb als Ausbruch. (0 .. 200) |

### VP / channel: colours  (drawer group "Kanal: Farben")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `KA_COL_TREND` | 90,150,255 | all charts | Trend-Kanal: Farbe Trend-Kanal. (Farbe R,G,B) |
| `KA_COL_RANGE` | 230,130,60 | all charts | Range-Kanal: Farbe Range-Kanal. (Farbe R,G,B) |
| `KA_PROF_COL_VOL` | 128,128,128 | all charts | Profil Volumen: Profil: Farbe Volumen. (Farbe R,G,B) |
| `KA_PROF_COL_POC` | 200,40,200 | all charts | Profil POC / Wanderung: Profil: POC und POC-Wanderung. (Farbe R,G,B) |
| `KA_PROF_COL_VA` | 64,224,208 | all charts | Profil VAH / VAL: Profil: VAH und VAL. (Farbe R,G,B) |
| `KA_PROF_COL_VWAP` | 255,140,0 | all charts | Profil VWAP: Profil: VWAP. (Farbe R,G,B) |
| `KA_PROF_COL_PLUS` | 40,80,255 | all charts | Profil Delta plus: Profil: Delta plus (Ask). (Farbe R,G,B) |
| `KA_PROF_COL_MINUS` | 220,40,40 | all charts | Profil Delta minus: Profil: Delta minus (Bid). (Farbe R,G,B) |
| `KA_PROF_COL_INFO` | 220,220,220 | all charts | Profil Infoblock: Profil: Schrift des Infoblocks. (Farbe R,G,B) |
| `KA_COL_MARKE` | 235,235,235 | all charts | Marke: Marken am Bar (Kuerzel aus Kuerzel.txt); unbekannte Woerter grau, gewaehlte Marke gelb. (Farbe R,G,B) |

### VP / channel: display  (drawer group "Kanal: Darstellung")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `KA_TEXTSIZE` | 9 | per chart | Schriftgroesse: Schriftgroesse der Kanal-Beschriftung, der Kuerzel und des Infoblocks. (6 .. 30) |

### VP / channel: profile  (drawer group "Kanal: Profil")

| Key | Value | Scope | Description (German) |
|---|---|---|---|
| `KA_KANAL_NEU` | 0 | all charts | Kanal bei neuen VPs: Regressionskanal (Linien und Beschriftung) bei neuen VPs zeigen; je VP in der Kachel umschaltbar. Gerechnet wird er immer (Typ, Lage). (1 = an, 0 = aus) |
| `KA_PROF_VA` | 40 | per market | Value Area %: Value Area des Kanal-Profils. (10 .. 99) |
| `KA_ANSICHT` | 0 | per chart | Ansicht der VPs: Profil = Volumenprofil, Kanal (falls an), Marken; Rahmen = nur duenner Rahmen mit Nummer (Marken aus). Vorgabe: nur das gewaehlte und angeheftete VP als Profil, alle anderen als Rahmen. (0 = gewaehltes + angeheftete als Profil, 1 = alle als Profil, 2 = alle als Rahmen) |
| `KA_PROF_WIDTH` | 20 | per chart | Profil Breite %: Breite des Profils in % der Kanalbreite. (5 .. 100) |
| `KA_PROF_COLMODE` | 1 | per chart | Profil Faerbung: Grau, grau mit Delta-Anteil (wie SC) oder nach Delta je Preis. (0 = grau, 1 = grau + Delta, 2 = Delta-Farbe) |
| `KA_PROF_WANDER` | 1 | per chart | POC-Wanderung: Laufender POC als Striche (ein Strich je Abschnitt mit gleichem POC). (1 = an, 0 = aus) |
| `KA_PROF_VWAP` | 1 | per chart | VWAP ab Kanalstart: VWAP des Kanals als Linie. (1 = an, 0 = aus) |
| `KA_PROF_INFO` | 1 | per chart | Infoblock: V, AV - BV, Rng, POCV ueber der linken oberen Ecke. (1 = an, 0 = aus) |
| `KA_PROF_UNTEN` | 2 | per chart | Unter dem Hauptchart: SC 'Draw Study Underneath Main Price Graph': Profil und Kanal-Zeichnung unter den Kerzen. Vorgabe 2. (0 = nein (ueber den Kerzen), 1 = 1, 2 = 2) |
| `KA_PROF_FRAME` | 1 | per chart | Rahmen: Gepunkteter Rahmen um den Kanal. (1 = an, 0 = aus) |
| `KA_PROF_EXPORT` | 0 | per chart | Profil-Werte in Datei: Je Kanal eine Zeile in Data/MTS/Kanal_Profile_<Symbol>_<Bar-Art>.csv. (1 = an, 0 = aus) |


---

## 10. Files

All MTS files are in `<SierraChart>\Data\MTS\`. `<Market>_<Bars>` = market and bar type of the chart, e.g. `ES_R14`.

| File | What | Notes |
|---|---|---|
| `Tagebuch_<Market>.csv` | your journal entries | **your data – back it up**; lines are appended and replayed on load |
| `Kanaele_<Market>.csv` | your VPs | **your data – back it up** |
| `Kuerzel.txt` | your code list | edit freely, re-read every 10 s |
| `Merkmale_<Market>_<Bars>.csv` | all compute-core values per bar (switch `EXPORT`) | lets MTS restore liquidity values after a restart |
| `Tagebuch_Werte_*`, `Tagebuch_Bars_*` | journal export | rewritten on every change |
| `Allgemein.txt` | interface, colours, Cockpit position, replay (all charts) | settings |
| `ES.txt`, `CL.txt` … | calculation and trade settings per market | settings |
| `Chart_<Market>_<Bars>.txt` | display settings per chart | a new bar type copies the last changed chart of that market |

Do not shorten the line files (`Tagebuch_*`, `Kanaele_*`) by hand while SC is running. To discard data: close SC, move the
files away, start SC.

---

## 11. Troubleshooting / FAQ

- **Nothing is shown by Numberbar/DOM/Cockpit:** MTS Rechenkern missing or not at the top of the study list.
- **Liquidity / net values grey or empty:** no market-depth data (historical bars without recorded depth, or a feed
  without depth). Use live data or replay with recorded market depth.
- **DOM or Cockpit not visible:** show SC columns "General Purpose 1" (DOM) / "General Purpose 2" (Cockpit) via Trade >>
  Customize Chart/Trade DOM Columns and drag their width.
- **Settings can't be changed:** the drawer is locked – click the lock first.
- **Footprint shows fewer bars than set:** limited by `FOOTPRINT_BARS` in "Compute core: footprint".
- **Orders are not sent:** Auto Trading Enabled is per chart; live accounts need "allow live orders"; quantity above max
  position is refused; grey buttons = sending not possible right now.
- **"?" opens nothing:** Windows needs a program associated with .md files (e.g. VS Code, Notepad++, Typora).
- **Two charts of the same market show different journal entries for a moment:** they sync within about 2 seconds.
- **After changing the bar type the footprint/liquidity history is empty:** compute-core data is kept per bar type; it
  rebuilds from new data.

---

## 12. Glossary (German interface terms → English)

| German | English |
|---|---|
| Rechenkern | compute core |
| Einstellungen | settings (drawer) |
| Kachel | tile |
| Tagebuch | journal |
| Kuerzel | code |
| Kanal | channel |
| VP setzen / beenden / loeschen | set / end / delete VP |
| immer zeigen | pin / always show |
| Netto | net change |
| Buch | book |
| Nehmen / Nicht traden | take / no trade (signal tile – not in the test version) |
| Liquiditaet | liquidity |
| Gesamt | total |
| Bar-Zeit | bar time |
| Menge | quantity |
| an / aus | on / off |
| Auf / Ab | up / down |
| Notiz | note |
| loeschen | delete |
| Schublade | drawer |
| Absorption am Extrem | absorption at the extreme |
| Seitenwechsel | side change |
| Event moeglich | event possible |
| ausgeglichen | balanced |
| Auswertung | session stats |
| Aufnahme | recording (author's tool, not part of MTS) |
