---
applyTo: "**/*.html, **/*.css, **/*.jsx, **/*.tsx, **/components/**, **/pages/**, **/views/**"
---

# Frontend Design Skill

Activate when: user asks to build web components, pages, landing pages, dashboards, React components, HTML/CSS layouts, or requests styling/beautification of any web UI.

## Design-First Thinking (Before Writing Code)

Answer these before touching code:
1. **Purpose** — What problem does this UI solve? Who uses it?
2. **Tone** — Pick an extreme and commit: brutally minimal, maximalist, retro-futuristic, organic, luxury, playful, editorial, brutalist, art deco, soft/pastel, industrial, etc.
3. **Differentiation** — What ONE thing will users remember about this interface?

**CRITICAL**: Choose a clear conceptual direction and execute it with precision. Both bold maximalism and refined minimalism work — the key is intentionality.

## Design Guidelines

### Typography — Be Distinctive
- ❌ Never use Inter, Roboto, Arial, or system fonts as the primary display font
- ❌ Never use Space Grotesk (overused AI output)
- ✅ Choose characterful, unexpected fonts — pair a display font with a refined body font
- Examples: Georgia + Calibri, Playfair Display + Source Sans, Bebas Neue + Open Sans

### Color & Theme
- Use CSS variables for consistency across the UI
- Dominant color (60–70% weight) + 1–2 supporting tones + 1 sharp accent
- ❌ Never use purple gradient on white — the default AI aesthetic
- Commit to a palette that feels designed for THIS specific context

### Motion & Interaction
- CSS-only animations preferred for HTML; Motion library for React
- One well-orchestrated page load with staggered reveals beats scattered micro-interactions
- Use scroll-triggered effects and hover states that surprise

### Spatial Composition
- Consider: asymmetry, overlap, diagonal flow, grid-breaking elements
- Generous negative space OR controlled density — never timid middle-ground
- Unexpected layouts beat safe, predictable grids

### Backgrounds & Depth
- Avoid flat solid color backgrounds
- Use: gradient meshes, noise textures, geometric patterns, layered transparencies
- Dramatic shadows, decorative borders, grain overlays — create atmosphere

## What to Avoid

- ❌ Generic "AI slop" aesthetics (purple gradients, Inter font, predictable card layouts)
- ❌ Same design choices across different generations / requests — vary everything
- ❌ Timid, evenly-distributed color palettes
- ❌ Plain white background with default font stack
- ❌ Cookie-cutter component patterns with no context-specific character

## Implementation Standards

- Production-grade and functional — not just pretty mockups
- Visually striking and memorable
- Cohesive aesthetic point-of-view throughout
- Meticulously refined in spacing, typography, and details
- Match code complexity to aesthetic vision:
  - Maximalist design → elaborate animations and effects
  - Minimalist design → restraint, precision, perfect spacing

## React-Specific

```jsx
// Prefer CSS variables for theming
const theme = {
  '--color-primary': '#1E2761',
  '--color-accent': '#F96167',
  '--font-display': "'Playfair Display', serif",
  '--font-body': "'Source Sans 3', sans-serif",
};

// Use Motion for animations
import { motion } from 'motion/react';
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.6, delay: index * 0.1 }}
>
```

## HTML/CSS-Specific

```css
/* Define design tokens as variables */
:root {
  --color-bg: #0f0f0f;
  --color-surface: #1a1a1a;
  --color-primary: #e8c547;
  --color-text: #f0ede8;
  --font-display: 'Bebas Neue', sans-serif;
  --font-body: 'IBM Plex Sans', sans-serif;
  --space-unit: 8px;
}

/* Staggered entrance animation */
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(24px); }
  to   { opacity: 1; transform: translateY(0); }
}
.item { animation: fadeUp 0.5s ease both; }
.item:nth-child(2) { animation-delay: 0.1s; }
.item:nth-child(3) { animation-delay: 0.2s; }
```

## Remember

Claude (and Copilot) are capable of extraordinary creative work. Don't default to safe choices — commit fully to a distinctive vision. No two interfaces should look the same.
