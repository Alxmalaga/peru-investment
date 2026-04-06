---
applyTo: "**/*.xlsx, **/*.xlsm, **/*.csv, **/*.tsv, **/excel/**, **/spreadsheet**"
---

# XLSX — Excel / Spreadsheet Skill

Activate when: user mentions .xlsx, .xlsm, .csv, .tsv, "spreadsheet", "Excel", or wants to create/edit/analyze tabular data with a spreadsheet deliverable.
Do NOT use when: primary output is a Word doc, HTML report, standalone Python script, or Google Sheets API integration.

## Tools by Task

| Task | Tool |
|------|------|
| Data analysis, bulk operations | `pandas` |
| Formulas, formatting, Excel features | `openpyxl` |
| Formula recalculation | `scripts/recalc.py` (LibreOffice) |

## CRITICAL: Always Use Excel Formulas — Never Hardcode Calculations

```python
# ❌ WRONG — hardcoding a Python-calculated value
total = df['Sales'].sum()
sheet['B10'] = total

# ✅ CORRECT — let Excel calculate it
sheet['B10'] = '=SUM(B2:B9)'
sheet['C5'] = '=(C4-C2)/C2'
sheet['D20'] = '=AVERAGE(D2:D19)'
```

## Standard Workflow

```python
# 1. Load / Create
from openpyxl import Workbook, load_workbook
import pandas as pd

wb = Workbook()                          # new
wb = load_workbook('existing.xlsx')      # existing

# 2. Modify
sheet = wb.active
sheet['A1'] = 'Header'
sheet['B2'] = '=SUM(B3:B10)'

# 3. Save
wb.save('output.xlsx')

# 4. Recalculate formulas (MANDATORY if using formulas)
# python scripts/recalc.py output.xlsx

# 5. Check for errors — fix any #REF!, #DIV/0!, #VALUE!, #NAME?
```

## Financial Model Standards

### Color Coding (Industry Standard)
- **Blue text** `RGB(0,0,255)` — hardcoded inputs / scenario assumptions
- **Black text** `RGB(0,0,0)` — all formulas and calculations
- **Green text** `RGB(0,128,0)` — links from other sheets in same workbook
- **Red text** `RGB(255,0,0)` — external file links
- **Yellow background** `RGB(255,255,0)` — key assumptions / cells needing update

### Number Formatting
- Years → text strings (`"2024"`, not `2,024`)
- Currency → `$#,##0` with units in headers (`"Revenue ($mm)"`)
- Zeros → `"$#,##0;($#,##0);-"` (show dash, not 0)
- Percentages → `0.0%`
- Multiples → `0.0x`
- Negatives → parentheses `(123)`, not minus `-123`

### Formula Rules
- All assumptions in dedicated input cells — use cell references, never hardcoded values
- Use `=B5*(1+$B$6)` not `=B5*1.05`
- Document hardcoded values: `Source: [System], [Date], [Reference]`

## Formula Verification Checklist

- [ ] Test 2–3 sample references before building full model
- [ ] Confirm Excel column mapping (column 64 = BL, not BK)
- [ ] Remember: Excel rows are 1-indexed (DataFrame row 5 = Excel row 6)
- [ ] Check for NaN / null values with `pd.notna()`
- [ ] Check for division by zero before using `/` in formulas
- [ ] Verify cross-sheet references use correct format: `Sheet1!A1`
- [ ] Run `scripts/recalc.py` and confirm status is `"success"`

## Code Style

- Write minimal Python — no unnecessary comments or verbose variable names
- Avoid redundant `print` statements
- Add cell comments for complex formulas
- Document data sources for hardcoded values

## Dependencies

- `pip install pandas openpyxl` — core libraries
- LibreOffice (`scripts/recalc.py`) — formula recalculation
