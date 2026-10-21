# Image to Code Skill (`image-to-code`)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Antigravity Compatible](https://img.shields.io/badge/Antigravity-Skill-purple.svg)](https://github.com/magebase/image-to-code-skill)

Autonomous, pixel-accurate UI reverse-engineering and code generation engine for **Antigravity CLI (`agy`)**, **Claude Code**, **Cursor**, and multi-agent systems. Transpiles visual reference images, Figma designs, wireframes, and screenshots into production-grade HTML/Tailwind, React, Vue, Svelte, or Django templates, powered by an automated multi-phase **LLM visual similarity & discrepancy analysis loop** using `read_image` / multimodal inspection to achieve $\ge 95\%$ visual fidelity before release.

---

## Architecture: The 5-Phase Closed Loop

```
[ Reference Image ] ──► [ Phase 1: Visual Token Decomposition ]
                                  │
                                  ▼
                        [ Phase 2: Code Synthesis ]
                                  │
                                  ▼
                        [ Phase 3: Headless Visual Capture ]
                                  │
                                  ▼
                        [ Phase 4: LLM Multimodal Similarity & Discrepancy Analysis ]
                                  │
                                  ├────────── (S_sim < 95%) ──► [ Phase 5: Auto-Remediation ] ──┐
                                  │                                                             │
                                  ▼ (S_sim >= 95%)                                             │
                        [ Certified Code + Sign-Off ] ◄─────────────────────────────────────────┘
```

---

## Key Features

1. **Pixel-Level Reverse Engineering:** Extracts exact viewport bounds, padding rhythms, typography hierarchies, and surface color palettes from any UI screenshot or design mock.
2. **Headless Browser Rendering:** Built-in Playwright capture engine (`scripts/render_screenshot.py`) renders code at exact viewports (Desktop `1440x900`, Tablet `768x1024`, Mobile `375x812`).
3. **Multimodal Discrepancy Analysis:** Calculates the Composite Visual Similarity Index ($S_{sim}$) and highlights spatial discrepancies in a high-contrast magenta diff overlay (`scripts/visual_compare.py`).
4. **Structured Discrepancy Matrix:** Categorizes defects by Critical, Major, and Minor tiers with copy-paste Tailwind/CSS remediation patches.
5. **Anti-Slop Invariants:** Enforces clean vector SVGs, semantic HTML5, accessible WAI-ARIA patterns, and strictly prohibits Unicode emojis across production UI.

---

## Installation

### 1. Install into Antigravity CLI (`agy`)
Clone or symlink the repository into your global or project skills directory:

```bash
# Global Antigravity installation (recommended)
git clone https://github.com/magebase/image-to-code-skill.git /home/aqua/skills/image-to-code-skill
ln -sf /home/aqua/skills/image-to-code-skill ~/.gemini/config/skills/image-to-code

# Workspace installation (project-specific)
mkdir -p .agents/skills
ln -sf /home/aqua/skills/image-to-code-skill .agents/skills/image-to-code
```

### 2. Install Python Dependencies
```bash
pip install Pillow playwright
playwright install chromium
```

---

## Quickstart & CLI Usage

### Step 1: Render your candidate code
```bash
python3 scripts/render_screenshot.py \
  --input components/hero.html \
  --output artifacts/candidate.png \
  --viewport 1440x900
```

### Step 2: Run Visual Comparison
```bash
python3 scripts/visual_compare.py \
  --reference designs/hero_mockup.png \
  --candidate artifacts/candidate.png \
  --output-diff artifacts/diff_overlay.png \
  --output-triptych artifacts/tri_comparison.png
```

Sample output:
```text
=======================================================
  IMAGE-TO-CODE VISUAL VERIFICATION: [PASS]
=======================================================
Visual Similarity Score: 96.4% (Target: >= 95.0%)
Reference:  1440x900 -> designs/hero_mockup.png
Candidate:  1440x900 -> artifacts/candidate.png
Discrepancy Regions Detected: 1
Diff Overlay Saved: artifacts/diff_overlay.png
Triptych Saved:     artifacts/tri_comparison.png
-------------------------------------------------------
  * Region #1: top=12.5%, left=82.0%, width=10.0%, height=4.2%
=======================================================
```

---

## Repository Structure

```
image-to-code-skill/
├── SKILL.md                                 # Core Antigravity skill definition & instructions
├── README.md                                # Skill documentation
├── LICENSE                                  # MIT License
├── scripts/
│   ├── render_screenshot.py                 # Headless Playwright screenshot capture engine
│   └── visual_compare.py                    # Perceptual diff & similarity calculation tool
├── references/
│   ├── visual-discrepancy-rubric.md         # 100-point visual inspection grading criteria
│   ├── tailwind-design-tokens-cheat-sheet.md# Pixel-to-Tailwind spacing/typography cheatsheet
│   └── llm-vision-prompts.md                # System prompts for multimodal LLM visual inspection
├── templates/
│   └── similarity_report_template.md        # Standardized Markdown visual report template
└── assets/
    └── .gitkeep                             # Asset placeholders
```

---

## License

MIT © [MageBase](https://github.com/magebase)
