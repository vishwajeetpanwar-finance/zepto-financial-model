# How to Build a 3-Statement + DCF Model (Zepto, step by step)

Open a blank Excel workbook alongside this guide. We'll build it tab by tab, in the same order a real analyst would. Every step says **what** to build and **why** — the "why" is the part that actually transfers to your next model.

---

## Step 0: Before you open Excel — get real data

Never start with formulas. Start with facts. For Zepto:
- Search for the company's DRHP (Draft Red Herring Prospectus) — since it's IPO-bound, this is a public regulatory filing with audited financials.
- Pull the last 3-4 years of: Revenue, Net Income/Loss, and any expense lines that are disclosed.
- Note what is **not** disclosed. You'll need modeled assumptions for those gaps — and you should always know which numbers are real vs. estimated.

**Why this matters:** A model built on invented numbers is a spreadsheet exercise. A model built on real anchors, with clearly labeled assumptions filling the gaps, is what analysts actually do — and it's what makes the project defensible in an interview ("how did you get this number?").

For Zepto, the real anchors were:

| FY | Revenue (₹ Cr) | Net Loss (₹ Cr) |
|---|---|---|
| FY23A | 2,025 | 1,272 |
| FY24A | 4,454 | 1,215 |
| FY25A | 11,110 | 4,700 |
| FY26A | 22,624 | 5,905 |

---

## Step 1: Set up the workbook shell

1. Create 6 tabs, in this order: **Summary, Assumptions, Income Statement, Balance Sheet, Cash Flow, DCF**.
2. In each statement tab, put years across the columns: `B` through `L` = FY23A, FY24A, FY25A, FY26A (actuals), then FY27E through FY33E (7 projected years). Row labels go in column A.
3. Adopt a color convention and *stick to it religiously*:
   - **Blue font** = hardcoded input (a number you typed in)
   - **Black font** = a formula that stays within the same sheet
   - **Green font** = a formula that links to a *different* sheet
   - **Yellow fill** = a key output or assumption you especially want to be able to find later

**Why:** The color code is how anyone (including you, in six months) can tell at a glance whether a number is an assumption, a calculation, or a cross-sheet link — without hunting through formulas.

4. Freeze panes at cell B5 on each sheet, so row/column headers stay visible as you scroll.

---

## Step 2: Build the Assumptions tab first — always

This is the single most important habit: **every number the model needs should live in one place, in Assumptions, and every other formula should reference it — never re-type a growth rate or a margin assumption directly into a formula.**

Build these rows:

**A. Revenue**
- Row: `Revenue (₹ Cr)` — hardcode the 4 actual years in blue. For projected years, formula: `=PriorYearCell*(1+GrowthRateCell)`
- Row: `Revenue growth % YoY` — for historicals, compute it: `=ThisYear/LastYear-1` (this is a sanity check, showing you the growth the actuals imply). For projections, hardcode your assumed growth rate — this is a lever you'll flex later. A sensible glide path decelerates over time (e.g., 55% → 12%) because no company keeps growing at its early-stage rate forever.

**B. Cost structure, as % of revenue**
- One row each for: COGS%, Delivery cost%, Employee cost%, Marketing%, Tech%, G&A%.
- Where you have a *disclosed* expense line (e.g., "Delivery & handling cost" in the DRHP), back into the % by dividing the disclosed rupee amount by revenue for that year — that's a real, defensible number.
- Where nothing is disclosed, you estimate — and you should comment the cell explaining your reasoning (e.g., "quick-commerce COGS typically 74-82% of revenue for grocery").
- Project these percentages *improving* over time (operating leverage) — that's the story of how a loss-making company reaches profitability.

**C. D&A, Capex, Tax, Working Capital**
- D&A as % of revenue
- Capex as % of revenue
- Tax rate (use the real statutory corporate rate for the country)
- Days Sales Outstanding (receivables), Days Payable Outstanding, Inventory Days — these drive the working-capital lines on the balance sheet later.
- A "minimum cash balance" — this becomes important in the Cash Flow tab.

**D. WACC and terminal growth (needed for the DCF later)**
- Risk-free rate (current government bond yield)
- Equity risk premium
- Beta
- Cost of equity = `Risk-free + Beta*ERP` (CAPM)
- Cost of debt, and after-tax cost of debt = `Pre-tax cost*(1-tax rate)`
- Target % equity / % debt in the capital structure
- WACC = `%Equity*CostOfEquity + %Debt*AfterTaxCostOfDebt`
- Terminal growth rate (usually close to long-run nominal GDP growth)

**Why do this before the Income Statement:** If you build the assumptions after you've already written formulas into the Income Statement, you'll end up hardcoding numbers directly into IS formulas — the exact thing the color convention is meant to prevent.

---

## Step 3: Build the Income Statement

Every line here should be a formula pulling from Assumptions — nothing hardcoded except the plug described below.

```
Revenue                    = Assumptions!Revenue row
COGS                       = -Revenue * Assumptions!COGS%
Gross Profit               = Revenue + COGS
Delivery & handling        = -Revenue * Assumptions!Delivery%
Employee benefits          = -Revenue * Assumptions!Employee%
Marketing                  = -Revenue * Assumptions!Marketing%
Technology                 = -Revenue * Assumptions!Tech%
G&A / other opex           = (see below — historical is a plug)
Total Opex                 = SUM of the five lines above
EBITDA                     = Gross Profit + Total Opex  (Opex lines are negative, so this is really subtraction)
D&A                        = -Revenue * Assumptions!D&A%
EBIT                       = EBITDA + D&A
Other income/(expense)     = 0 for historicals; for projections, ~0.5% * prior year's cash balance (interest income)
PBT                        = EBIT + Other income
Tax expense                = -MAX(PBT,0) * Assumptions!Tax rate      ← only taxed when profitable
Net Income                 = PBT + Tax expense
```

**The historical G&A "plug" — an important technique.** For FY23A-FY26A, you know the actual reported Net Income (your real anchor), but you don't have a full expense breakdown. So: compute every other line using your assumed percentages, then work backwards — solve for whatever G&A number makes Net Income equal to the actual reported loss. This is standard practice when disclosure is incomplete: you tie to what's real, and treat the unknown residual as a plug, clearly documented. For projected years, G&A becomes a normal assumption-driven line instead (declining % of revenue).

To solve the historical plug by hand: `G&A = Gross Profit - (Delivery+Employee+Marketing+Tech) - D&A - Actual Net Loss`

---

## Step 4: Build the Balance Sheet

This is where most model-builders get tripped up, so go slowly.

**Assets**
```
Cash                  = link from Cash Flow's "ending cash" row (built in Step 5)
Receivables           = Revenue / 365 * Assumptions!DSO
Inventory             = -COGS / 365 * Assumptions!Inventory Days
Other current assets  = small % of revenue (modeled)
PP&E, net             = Prior year PP&E + Capex + D&A   (D&A is stored as negative, so this is capex minus depreciation)
Other non-current     = small % of revenue (modeled)
```

**Liabilities**
```
Payables                     = -COGS / 365 * Assumptions!DPO
Other current liabilities    = small % of revenue (modeled)
Debt                          = 0 (Zepto is equity-funded; adjust if your company carries debt)
```

**Equity**
```
Share capital     = Prior year + equity raised this period (this comes from the Cash Flow's funding plug)
Retained earnings = Prior year + this year's Net Income
```

**Critical rule: apply the SAME formula to every column, historical and projected alike.** Don't hardcode "reasonable-looking" historical balance sheet numbers and then switch to formulas for projections — I made exactly this mistake on the first pass and it broke the balance check by ~₹8,500 Cr. If PP&E, working capital, and other lines all use one consistent formula from a zero starting base, the whole thing self-balances by construction.

**The balance check.** At the bottom, add: `= Total Assets - (Total Liabilities + Total Equity)`. This must read **exactly zero** in every single column. If it doesn't, you have a formula inconsistency somewhere — almost always because one line on the asset side changed without a matching cash-flow entry recording where that change came from (see Step 5).

---

## Step 5: Build the Cash Flow Statement

This is the statement that *proves* your balance sheet is internally consistent — every asset or liability that changes must show up here.

**Operations**
```
Net Income
+ D&A
– Increase in Receivables
– Increase in Inventory
– Increase in Other Current Assets
+ Increase in Payables
+ Increase in Other Current Liabilities
= Cash Flow from Operations
```

**Investing**
```
– Capex
– Increase in Other Non-Current Assets     ← easy to forget! this must be here or the balance sheet won't tie
= Cash Flow from Investing
```

**Financing — the funding plug**
```
Beginning cash
+ External equity raised (the "plug")
+ Debt raised/(repaid)  [0 here]
= Cash Flow from Financing
```

The plug formula is the clever part:
```
Equity raised = MAX(0, MinimumCashBalance – (BeginningCash + CFO + CFI))
```
In plain English: "if this year's operating and investing cash flows would push the cash balance below our minimum floor, raise exactly enough equity to top it back up to that floor — otherwise raise nothing." This mirrors what a real cash-burning startup actually does (Zepto raised round after round rather than letting cash go negative), and it keeps every projected year's cash balance realistic instead of going deeply negative on paper.

```
Net change in cash = CFO + CFI + CFF
Ending cash        = Beginning cash + Net change in cash
```

Then go back to the Balance Sheet and point the Cash row at this Ending Cash line.

**Once this is done, go check your balance check row from Step 4. It should now read zero everywhere.**

---

## Step 6: Build the DCF

Only the projected years (FY27E-FY33E) matter here — the DCF values future cash flows, so historicals are just context.

**Unlevered Free Cash Flow**
```
EBIT
– Tax on EBIT (unlevered)     = -MAX(EBIT,0) * tax rate
= NOPAT
+ D&A
– Capex
– Increase in Net Working Capital   (sum of the WC lines from the Cash Flow tab)
= Unlevered Free Cash Flow
```

**Discounting**
```
Discount period  = 1, 2, 3... 7 (years from today to each forecast year)
Discount factor  = 1 / (1+WACC)^period
PV of FCF        = FCF * Discount factor
```

**Terminal value** (Gordon Growth — assumes the business grows at a stable rate forever after the explicit forecast ends)
```
Terminal Value = Final year FCF * (1+terminal growth) / (WACC – terminal growth)
PV of Terminal Value = Terminal Value * final year's discount factor
```

**Putting it together**
```
Enterprise Value = Sum of PV(FCF) + PV(Terminal Value)
Equity Value      = Enterprise Value – Net Debt (Debt – Cash), using the latest actual balance sheet
```
Convert to USD if useful: `= Equity Value (₹ Cr) / USD-INR rate / 100` — remember 1 crore = 10 million, so crore→billion needs ÷100 in the exchange step, not ÷1000. (This exact bug cost me a 10x error on the first pass — always sanity-check unit conversions against a real-world benchmark.)

**Sanity-check your output** against something real: a comparable valuation, a recent funding round, an IPO price. If your DCF comes out wildly different, that's not necessarily wrong — a conservative DCF often lands below what a hyped private market pays for growth/optionality — but you should be able to explain the gap, not just report the number.

---

## Step 7: Add a sensitivity table

DCF outputs are extremely sensitive to WACC and terminal growth — a good analyst always shows a range, not a single point estimate.

Build a small grid: WACC values down the rows (e.g., 12%, 13.5%, 15%, 16.5%, 18%), terminal growth across the columns (e.g., 3%, 4%, 5%, 6%, 7%). In each cell, recompute Enterprise Value using `SUMPRODUCT` to discount the FCF array at that row's WACC, plus a terminal value term using that row's WACC and that column's growth rate. This lets you see at a glance how much your valuation swings, and it's usually the first thing an interviewer asks about.

---

## Step 8: Final QC pass

Before you call it done:
1. **Recalculate everything** and scan for `#REF!`, `#DIV/0!`, `#NAME?` errors.
2. **Confirm the balance sheet check row is zero in every column.**
3. **Confirm historical Net Income matches the real reported figures exactly** (this validates your plug logic worked).
4. **Trace 2-3 formulas by hand** — pick a random cell, compute what it should be with a calculator, compare. Never trust a clean recalc alone; it proves formulas *evaluate*, not that they're *right*.
5. **Comment every assumption** you invented, with your reasoning, so anyone reading the file (including future you) can tell real data from modeled estimates at a glance.

---

## What to practice next

Once you've rebuilt this once by hand, try it on a different company — ideally one with a full disclosed income statement (a listed company's 10-K/annual report), so you don't need any plugs. That will show you the "clean" version of this exercise before you tackle another private company with gaps to fill.
