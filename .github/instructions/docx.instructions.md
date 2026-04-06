---
applyTo: "**/*.docx, **/docx/**, **/*word*, **/*document*"
---

# DOCX — Word Document Skill

Activate when the user mentions: Word doc, .docx, report, memo, letter, template, tracked changes, or table of contents.
Do NOT use for PDFs, spreadsheets, or Google Docs.

## Quick Reference

| Task | Approach |
|------|----------|
| Read content | `pandoc` or unpack for raw XML |
| Create new document | Use `docx` npm package (JS) |
| Edit existing document | Unpack → edit XML → repack |
| Convert .doc → .docx | `soffice --headless --convert-to docx file.doc` |

## Creating New Documents — JS with `docx`

```bash
npm install -g docx
```

```javascript
const { Document, Packer, Paragraph, TextRun, Table, TableRow, TableCell,
        HeadingLevel, AlignmentType, LevelFormat, BorderStyle, WidthType,
        ShadingType, PageNumber, PageBreak, PageOrientation } = require('docx');

const doc = new Document({ sections: [{ children: [] }] });
Packer.toBuffer(doc).then(buf => fs.writeFileSync("out.docx", buf));
```

### Critical Rules

- **Page size**: Always set explicitly — default is A4. US Letter = `width: 12240, height: 15840` (DXA units, 1440 DXA = 1 inch)
- **Landscape**: Pass portrait dimensions + `orientation: PageOrientation.LANDSCAPE` — docx-js swaps internally
- **Never use `\n`** — use separate `Paragraph` elements
- **Never use unicode bullets** (`•`, `\u2022`) — use `LevelFormat.BULLET` with numbering config
- **PageBreak must be inside a Paragraph** — standalone creates invalid XML
- **ImageRun requires `type`** — always specify `png`, `jpg`, etc.
- **Tables need dual widths**: `columnWidths` on the table AND `width` on each cell, both in DXA
- **Always use `WidthType.DXA`** — never `WidthType.PERCENTAGE` (breaks in Google Docs)
- **Use `ShadingType.CLEAR`** — never `SOLID` for cell shading
- **Never use tables as dividers** — use `border.bottom` on a Paragraph instead
- **Override built-in styles by exact ID**: `"Heading1"`, `"Heading2"`, etc.
- **Include `outlineLevel`** in heading styles for TOC (0 = H1, 1 = H2)

### Lists (Correct Pattern)

```javascript
// ❌ WRONG
new Paragraph({ children: [new TextRun("• Item")] })

// ✅ CORRECT
const doc = new Document({
  numbering: {
    config: [{
      reference: "bullets",
      levels: [{ level: 0, format: LevelFormat.BULLET, text: "•",
        alignment: AlignmentType.LEFT,
        style: { paragraph: { indent: { left: 720, hanging: 360 } } } }]
    }]
  }
});
new Paragraph({ numbering: { reference: "bullets", level: 0 }, children: [new TextRun("Item")] })
```

### Tables (Correct Pattern)

```javascript
// Tables need BOTH columnWidths AND width on each cell
const border = { style: BorderStyle.SINGLE, size: 1, color: "CCCCCC" };
new Table({
  width: { size: 9360, type: WidthType.DXA },
  columnWidths: [4680, 4680],  // Must sum to table width
  rows: [new TableRow({ children: [
    new TableCell({
      borders: { top: border, bottom: border, left: border, right: border },
      width: { size: 4680, type: WidthType.DXA },  // Also on each cell
      shading: { fill: "D5E8F0", type: ShadingType.CLEAR },
      margins: { top: 80, bottom: 80, left: 120, right: 120 },
      children: [new Paragraph({ children: [new TextRun("Cell")] })]
    })
  ]})]
})
```

## Editing Existing Documents (3-Step Workflow)

```bash
# Step 1: Unpack
python scripts/office/unpack.py document.docx unpacked/

# Step 2: Edit XML in unpacked/word/
# Use string replacement directly — no Python scripts needed
# Use smart quotes as XML entities: &#x2018; &#x2019; &#x201C; &#x201D;
# Author for tracked changes: "Claude"

# Step 3: Pack
python scripts/office/pack.py unpacked/ output.docx --original document.docx
```

## Validation

```bash
python scripts/office/validate.py doc.docx
```

## Dependencies

- `npm install -g docx` — create new documents
- `pandoc` — text extraction
- LibreOffice (`soffice`) — PDF/format conversion
- Poppler (`pdftoppm`) — PDF to images
