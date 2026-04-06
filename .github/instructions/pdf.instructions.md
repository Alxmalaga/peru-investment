---
applyTo: "**/*.pdf, **/pdf/**, **/*report*.py, **/*invoice*"
---

# PDF — PDF Processing Skill

Activate when: user mentions .pdf, needs to read/extract/merge/split/create/watermark/encrypt/OCR a PDF, or references PDF forms.

## Quick Reference

| Task | Best Tool |
|------|-----------|
| Merge PDFs | `pypdf` |
| Split PDFs | `pypdf` |
| Extract text | `pdfplumber` |
| Extract tables | `pdfplumber` |
| Create PDFs | `reportlab` |
| CLI merge | `qpdf` |
| OCR scanned PDFs | `pytesseract` + `pdf2image` |
| Fill PDF forms | See FORMS.md / `pdf-lib` |

## Install

```bash
pip install pypdf pdfplumber reportlab --break-system-packages
pip install pytesseract pdf2image  # for OCR
```

## Core Operations

### Read / Extract Text

```python
from pypdf import PdfReader
import pdfplumber

# Basic extraction
reader = PdfReader("document.pdf")
text = "".join(page.extract_text() for page in reader.pages)

# Layout-preserving extraction (better for complex docs)
with pdfplumber.open("document.pdf") as pdf:
    for page in pdf.pages:
        print(page.extract_text())
```

### Extract Tables

```python
import pdfplumber, pandas as pd

with pdfplumber.open("document.pdf") as pdf:
    all_tables = []
    for page in pdf.pages:
        for table in page.extract_tables():
            if table:
                df = pd.DataFrame(table[1:], columns=table[0])
                all_tables.append(df)

combined = pd.concat(all_tables, ignore_index=True)
combined.to_excel("extracted_tables.xlsx", index=False)
```

### Merge PDFs

```python
from pypdf import PdfWriter, PdfReader

writer = PdfWriter()
for pdf_file in ["doc1.pdf", "doc2.pdf"]:
    for page in PdfReader(pdf_file).pages:
        writer.add_page(page)

with open("merged.pdf", "wb") as f:
    writer.write(f)
```

### Split PDF

```python
reader = PdfReader("input.pdf")
for i, page in enumerate(reader.pages):
    writer = PdfWriter()
    writer.add_page(page)
    with open(f"page_{i+1}.pdf", "wb") as f:
        writer.write(f)
```

### Create PDF (reportlab)

```python
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer
from reportlab.lib.styles import getSampleStyleSheet

doc = SimpleDocTemplate("report.pdf", pagesize=letter)
styles = getSampleStyleSheet()
story = [
    Paragraph("Title", styles['Title']),
    Spacer(1, 12),
    Paragraph("Body content here.", styles['Normal']),
]
doc.build(story)
```

**IMPORTANT — Subscripts/Superscripts in reportlab:**
Never use Unicode subscript characters (`₀₁₂`, `⁰¹²`) — they render as black boxes in built-in fonts.
Use XML tags instead:
```python
Paragraph("H<sub>2</sub>O and x<super>2</super>", styles['Normal'])
```

### OCR Scanned PDFs

```python
import pytesseract
from pdf2image import convert_from_path

images = convert_from_path('scanned.pdf')
text = ""
for i, image in enumerate(images):
    text += f"\n--- Page {i+1} ---\n"
    text += pytesseract.image_to_string(image)
```

### Add Watermark

```python
from pypdf import PdfReader, PdfWriter

watermark = PdfReader("watermark.pdf").pages[0]
reader = PdfReader("document.pdf")
writer = PdfWriter()
for page in reader.pages:
    page.merge_page(watermark)
    writer.add_page(page)
with open("watermarked.pdf", "wb") as f:
    writer.write(f)
```

### Encrypt / Password Protect

```python
from pypdf import PdfWriter, PdfReader

writer = PdfWriter()
for page in PdfReader("input.pdf").pages:
    writer.add_page(page)
writer.encrypt("userpassword", "ownerpassword")
with open("encrypted.pdf", "wb") as f:
    writer.write(f)
```

## CLI Tools

```bash
# Merge
qpdf --empty --pages file1.pdf file2.pdf -- merged.pdf

# Split pages 1-5
qpdf input.pdf --pages . 1-5 -- pages1-5.pdf

# Rotate page 1 by 90°
qpdf input.pdf output.pdf --rotate=+90:1

# Remove password
qpdf --password=mypassword --decrypt encrypted.pdf decrypted.pdf

# Extract text preserving layout
pdftotext -layout input.pdf output.txt

# Extract images
pdfimages -j input.pdf prefix
```
