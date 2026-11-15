# Nomme-moi ! — IUPAC Nomenclature Trainer

A single-file, zero-dependency web game for practicing IUPAC organic nomenclature. Students observe a molecule drawn in SVG and type its systematic French name. The app provides instant diff-based feedback, tracks progress locally, and runs entirely offline.

> **Live:** open `index.html` in any modern browser. No build step, no server, no install.

---

## Features

- **45 curated compounds** spanning alkanes, cycloalkanes, alkenes, dienes, and mixed-functionality exam questions (alcohols, ketones, aldehydes, acids, esters, amides, nitriles, amines, halides, ethers, aromatics).
- **Interactive carbon numbering** — tap/click carbons to build the main chain; the app highlights numbered atoms and bonds in green. Nearest-atom picking ensures reliable touch targeting on phones.
- **Diff-based scoring** — character-level alignment shows exactly what was missed or added, with a percentage score. Answers too far from correct are flagged as fully wrong to prevent partial-credit gaming.
- **Hint system** — reveals the first 4 characters of the answer; using it is recorded and displayed in results.
- **Pause/resume** with blurred molecule to prevent peeking.
- **Persistent progress** via `localStorage` (best score per compound).
- **Shareable result card** — generates a PNG screenshot; uses the native Web Share API on mobile when available.
- **Fully responsive** — desktop-first layout that adapts to phones (≥360 px) with keyboard-aware resizing, safe-area support, and touch-optimized controls.
- **Zero dependencies** — one HTML file, inline CSS/JS, Google Fonts loaded via `<link>`. Works offline after first load (fonts cached by browser).

---

## Quick Start

```bash
# Just open the file
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows

# Or serve locally (optional, for CORS-free font loading)
npx serve .
python3 -m http.server 8000
```

No `npm install`, no bundler, no config.

---

## How to Play

1. Click **Jouer aléatoire** or pick a compound from the ☰ menu.
2. Study the molecule. Tap carbons to number the main chain (optional but helpful).
3. Type the IUPAC name in the input field. Accents (é, è, ê) are required.
4. Press **Vérifier** (or Enter). Review the colored diff, your score, and the explanation.
5. Use **Partager** to save/share your result, or **Jouer un autre aléatoire** to continue.

---

## Adding a New Compound

All data lives in the `<script id="data" type="application/json">` block. Each compound is a JSON object:

```jsonc
{
  "id": 46,                              // unique integer
  "nom": "3-bromo-2-méthylpentane",      // expected French IUPAC name
  "categorie": "alcane_ramifie",         // see CAT map in JS
  "difficulte": 3,                       // 1–5 stars
  "atoms": [                             // el = "C" (hidden), or Br/Cl/O/N/F/I/S
    { "id": "a0", "el": "C", "x": -50, "y": 0 },
    { "id": "a1", "el": "C", "x": 0,   "y": 0 },
    { "id": "a2", "el": "Br","x": 50,  "y": 0 }
  ],
  "bonds": [                             // [fromId, toId, order(1|2|3)]
    ["a0", "a1", 1],
    ["a1", "a2", 1]
  ]
}
```

### Drawing tips
- Coordinates are in arbitrary SVG user units; the renderer auto-fits the viewBox.
- Carbon atoms (`el: "C"`) are rendered as invisible vertices (just bond endpoints). Heteroatoms display their symbol.
- Triple bonds: align all 4 involved atoms in a straight line (180°) for correct rendering.
- Keep bond lengths ~36–42 units for consistent styling.
- After adding, append the object to the `composes` array and reload.

### Optional fields for mixed-functionality compounds
```jsonc
"nom_en": "...",              // English name (for reference)
"structure": { "smiles": "...", "formule_brute": "..." },
"groupe_principal": "...",    // e.g., "cétone", "alcool"
"prefixes_declasses": [...],  // groups demoted to prefixes
"raisonnement": "...",        // explanation shown in results
"variantes_acceptees": [...]  // alternative accepted names
```

---

## Architecture

| Layer | Implementation |
|---|---|
| Rendering | Inline SVG generated per-frame from atom/bond data; viewBox auto-computed |
| State | Plain JS globals + `localStorage` for persistence |
| Scoring | Custom LCS-based diff with accent folding and whitespace normalization |
| Layout | CSS Grid/Flex with `@media (max-width: 899px)` mobile overrides |
| Keyboard | Visual Viewport API + CSS `--kb` variable for iOS keyboard compensation |
| Touch | Nearest-atom hit testing via `getScreenCTM()` inverse transform |
| Sharing | Canvas rasterization → Blob → `<a download>` fallback / `navigator.share` |

Everything is in one file by design: easy to distribute, embed in LMS platforms, or hand to students on a USB stick.

---

## Browser Support

| Browser | Version |
|---|---|
| Chrome / Edge | 90+ |
| Firefox | 88+ |
| Safari (iOS/macOS) | 14.5+ |
| Samsung Internet | 14+ |

Requires: ES6, CSS custom properties, `visualViewport` API, SVG `getScreenCTM()`. No polyfills included.

---

## Known Limitations

- **Stereochemistry not enforced** — R/S, E/Z, cis/trans are not checked. Names are validated as strings only.
- **French-only validation** — English names are stored for reference but not accepted as answers.
- **No undo for typing** — standard contenteditable behavior; use OS-level undo (Ctrl/Cmd+Z).
- **Font dependency** — Google Fonts loaded externally; falls back to system sans-serif/monospace offline.
- **Mobile landscape** — usable but not optimized; portrait is the primary target.

---

## License

This project is provided as-is for educational use. The compound dataset and UI are original. Google Fonts (Nunito, JetBrains Mono) are licensed under OFL/Apache 2.0 respectively.

---

## Credits

Built as a drill tool for organic chemistry students preparing for IUPAC nomenclature exams. The diff engine was designed specifically for chemical name comparison (handling hyphens, commas, locants, and accent-insensitive matching).
