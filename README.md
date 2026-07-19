# Zepto (Kiranakart Technologies Ltd) — 3-Statement Financial Model & DCF Valuation

A full financial model built from scratch on Zepto, India's quick-commerce grocery delivery company, ahead of its IPO — built as a portfolio project to practice real-world modeling: working from disclosed financials, documenting every assumption, and reconciling all three statements to tie out exactly.

## Why Zepto

Zepto is IPO-bound (updated DRHP filed with SEBI, June 2026), which means real audited financials are publicly available — rare for a company this young. That let this model be anchored in actual disclosed numbers instead of pure guesswork, which is what makes it a useful practice exercise rather than a template exercise.

**Actual disclosed financials (₹ Crore, per the DRHP):**

| FY | Revenue | Net Loss |
|---|---|---|
| FY23A | 2,025 | 1,272 |
| FY24A | 4,454 | 1,215 |
| FY25A | 11,110 | 4,700 |
| FY26A | 22,624 | 5,905 |

## What's in this repo

| File | Description |
|---|---|
| `Zepto_Financial_Model_DCF.xlsx` | The full live model — 6 tabs, fully formula-linked |
| `Zepto_Financial_Model_DCF.pdf` | Static PDF export of every tab, for quick viewing without opening Excel |
| `How_To_Build_This_Model.md` | A step-by-step teaching guide to rebuilding this model from scratch |

## Model structure

1. **Summary** — cover page with company snapshot, historical financials, and headline DCF outputs
2. **Assumptions** — every driver of the model in one place (growth, margins, working capital, WACC, terminal growth). Blue cells are hardcoded inputs; every non-obvious assumption is commented with its reasoning and source.
3. **Income Statement** — fully linked to Assumptions. Historicals (FY23A–FY26A) tie exactly to Zepto's actual reported revenue and net loss.
4. **Balance Sheet** — fully linked; the balance check row reads exactly **0** in every year, FY23A through FY33E.
5. **Cash Flow** — indirect method, including a funding-plug mechanism so projected cash never goes negative — mirroring Zepto's actual reliance on successive private funding rounds to fund cash burn.
6. **DCF** — unlevered free cash flow, WACC built up via CAPM, Gordon Growth terminal value, and a WACC × terminal-growth sensitivity grid.

## Methodology notes

- Where the DRHP discloses a specific expense line (e.g., delivery & handling, employee benefits), that figure is used directly to back into a % of revenue.
- Where no breakdown is disclosed (e.g., historical G&A), the model solves for a balancing "plug" so that modeled Net Income ties exactly to the actual reported net loss each year — a standard technique for modeling around incomplete disclosure, and it's flagged as such in the file.
- Every modeled (non-disclosed) assumption is documented in a cell comment, so it's easy to distinguish real data from analyst estimates throughout.

## Headline finding

The base-case DCF implies an equity value of **~$2.4bn** — a 60–66% discount to both Zepto's last private funding round ($7.0bn, October 2025) and its reported IPO price band (~$5.6–5.95bn). That gap is a useful illustration of a common pattern: DCFs of high-growth, still-unprofitable companies often value the business more conservatively than private markets do, which tend to price in growth optionality and strategic scarcity that a pure cash-flow model doesn't capture.

## Disclaimer

This is an independent educational/portfolio project, not investment advice or an official valuation. Several inputs are modeled analyst estimates rather than disclosed figures — see in-file comments for which is which.
