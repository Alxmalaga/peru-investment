---
applyTo: "**/*.pptx, **/slides/**, **/deck/**, **/presentation**"
---

# PPTX — PowerPoint / Presentation Skill

Activate when: user mentions .pptx, "deck", "slides", "presentation", or needs to create/edit/extract from a PowerPoint file.

## Quick Reference

| Task | Command |
|------|---------|
| Read content | `python -m markitdown presentation.pptx` |
| Visual overview | `python scripts/thumbnail.py presentation.pptx` |
| Raw XML access | `python scripts/office/unpack.py presentation.pptx unpacked/` |
| Create from scratch | Use `pptxgenjs` (Node) |
| Edit from template | Unpack → edit XML → repack |

## Install

```bash
npm install -g pptxgenjs
pip install "markitdown[pptx]" Pillow
```

## Design Principles — Never Make Boring Slides

### Before Starting: Pick a Bold Palette
- Dominant color (60–70% visual weight) + 1–2 supporting + 1 sharp accent
- Dark backgrounds for title/conclusion, light for content ("sandwich")
- Commit to ONE visual motif and repeat it throughout

### Color Palette Reference

| Theme | Primary | Secondary | Accent |
|-------|---------|-----------|--------|
| Midnight Executive | `1E2761` | `CADCFC` | `FFFFFF` |
| Coral Energy | `F96167` | `F9E795` | `2F3C7E` |
| Warm Terracotta | `B85042` | `E7E8D1` | `A7BEAE` |
| Ocean Gradient | `065A82` | `1C7293` | `21295C` |
| Cherry Bold | `990011` | `FCF6F5` | `2F3C7E` |

### Typography

| Element | Size |
|---------|------|
| Slide title | 36–44pt bold |
| Section header | 20–24pt bold |
| Body text | 14–16pt |
| Captions | 10–12pt muted |

Pair a display font with a clean body font (e.g., Georgia + Calibri, Cambria + Calibri).

### Layout Options per Slide
- Two-column (text left, visual right)
- Icon + text rows (icon in colored circle, bold header, description)
- 2×2 or 2×3 grid
- Half-bleed image with content overlay
- Large stat callouts (60–72pt number + small label)

### Avoid (Critical)
- ❌ Text-only slides — always add image, icon, chart, or shape
- ❌ Same layout repeated across slides
- ❌ Center-aligned body text
- ❌ Accent lines under titles (hallmark of AI-generated slides)
- ❌ Default blue / purple gradient on white
- ❌ Generic fonts (Inter, Roboto, Arial for display)
- ❌ Low-contrast text or icons

## QA (Required — Assume Problems Exist)

```bash
# Content QA
python -m markitdown output.pptx

# Check for leftover placeholder text
python -m markitdown output.pptx | grep -iE "\bx{3,}\b|lorem|ipsum|\bTODO|\[insert"

# Convert to images for visual QA
python scripts/office/soffice.py --headless --convert-to pdf output.pptx
rm -f slide-*.jpg
pdftoppm -jpeg -r 150 output.pdf slide
ls -1 "$PWD"/slide-*.jpg
```

### Visual Inspection Checklist
- [ ] No overlapping elements
- [ ] No text cut off at edges
- [ ] No low-contrast text/icons
- [ ] No leftover placeholder content
- [ ] Consistent margins (≥ 0.5") and gaps (0.3–0.5" between blocks)
- [ ] Decorative lines fit all title lengths (not just single-line)
- [ ] No stacked/collision between footer and content above

**Do not declare success until completing at least one fix-and-verify cycle.**

## Dependencies

- `npm install -g pptxgenjs`
- `pip install "markitdown[pptx]" Pillow`
- LibreOffice — PDF conversion
- Poppler (`pdftoppm`) — PDF to images
