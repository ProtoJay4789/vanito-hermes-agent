---
name: brand-assets
description: "Create brand visual assets — logos, banners, thumbnails, avatars. SVG-first workflow with cairosvg for PNG export. Covers GenTech brand identity, social media assets, and GitHub portfolio images."
category: creative
version: 1.0.0
author: Gentech
tags: [brand, design, svg, logo, banner, thumbnail, avatar, visual-identity]
---

# Brand Assets — Visual Identity Creation

## When to Use
- Creating or updating logos, banners, profile pictures, thumbnails
- User says "change the photo", "new thumbnail", "make a banner"
- Any visual asset for GitHub, X/Twitter, Telegram, or portfolio

## Brand Identity Reference
Full brand spec: `Green-Room/designs/gentech-brand-identity.md` in vault.

### Core Elements
- **Name:** GenTech
- **Tagline:** "Tough love for the agent economy"
- **Logo:** The Seed mark (root + circuit gradient)
- **Colors:** Blue `#3b82f6` → Green `#22c55e` gradient on dark `#0a0a1a`
- **Voice:** Warm, direct, calm authority

### Color Palette
| Color | Hex | Use |
|-------|-----|-----|
| Deep Blue | `#3b82f6` | Primary |
| Green | `#22c55e` | Growth |
| Gold | `#f59e0b` | Value |
| Dark BG | `#0a0a1a` | Background |
| Soft White | `#e2e8f0` | Text |

## SVG-First Workflow

All brand assets are created as SVG first, then converted to PNG for deployment.

### Why SVG First
- Scales to any size without quality loss
- Editable as text (easy to tweak)
- Small file sizes
- cairosvg converts to PNG at any resolution

### Creation Process
1. Write SVG file with brand colors, gradients, typography
2. Convert to PNG with cairosvg: `cairosvg.svg2png(url=input.svg, write_to=output.png, output_width=W, output_height=H)`
3. Deploy PNG to GitHub Pages
4. Keep SVG as source of truth

### Standard Sizes
| Asset | SVG Viewport | PNG Size | Use |
|-------|-------------|----------|-----|
| Logo | 200×200 | 400×400 | Favicon, profile pic |
| Avatar | 400×400 | 400×400 | X/Twitter, Telegram profile |
| Banner | 1500×500 | 1500×500 | X/Twitter header |
| Thumbnail | 1200×630 | 1200×630 | GitHub social preview |
| Repo Thumbnail | 1200×630 | 1200×630 | README hero image |

## SVG Patterns

### Gradient Definitions
```xml
<linearGradient id="brandGrad" x1="0%" y1="0%" x2="100%" y2="0%">
  <stop offset="0%" style="stop-color:#3b82f6;stop-opacity:1"/>
  <stop offset="100%" style="stop-color:#22c55e;stop-opacity:1"/>
</linearGradient>
```

### Glow Filter
```xml
<filter id="glow">
  <feGaussianBlur stdDeviation="3" result="blur"/>
  <feMerge><feMergeNode in="blur"/><feMergeNode in="SourceGraphic"/></feMerge>
</filter>
```

### Dark Background with Grid
```xml
<rect width="W" height="H" fill="#0a0a1a"/>
<g stroke="#3b82f6" stroke-width="0.3" opacity="0.06">
  <!-- Grid lines every 100px -->
</g>
```

## Avatars — the small-size discipline (measured rules)

An avatar is not a small picture; it is a picture that must survive a **160px circular crop, on both a
dark and a light page**. Treat those as the design brief, not a final check.

**1. The platform crops to a CIRCLE — anything outside the inscribed circle does not exist.**
GitHub renders the avatar as a circle of diameter = image width. A mark on the subject's chest sits
exactly on that boundary and is sliced in half, which reads as a rendering artifact. **Settle the
mark's fate by geometry FIRST:** measure its distance from centre against the crop radius
(`sqrt(dx²+dy²) < W/2`). If it cannot fit inside, that is not a design problem to solve — it is an
impossible placement. Put the story in the bio and the profile README instead.

**2. Test at 160px on BOTH page backgrounds.** Downscale, mask circular, composite onto a dark page
(≈`#0d1117`) and a white one. The same file can read crisp on white and collapse into a moody smudge
on dark. A palette that fails the dark-mode composite fails the medium.

**3. Palette beats detail — and it is measurable.** Re-rendering an expression to make a portrait
"happier" moved the full-size review and NOT the 160px one; the small-size read was set by the
background. **Measure rather than argue:** sample the background band above the head and the face
region and compare mean luminance. Background luma ≈60 against a dark page reads "closed"; lifted to
≈110 it reads "open." **Fix the tonal collapse first** — it is one render and it is usually the
actual defect. Watch for the classic collapse: dark garment + dark hair + dark background = the lower
arc of the circle simply vanishes.

**4. An illegible text panel makes a critic HALLUCINATE — and that IS the test.** Asked to read a
named list at 160px, a vision model did not report "unreadable": it invented plausible different
text. **Treat an invented reading as proof of illegibility** — if a reviewer can confabulate content,
no human will read it. Cut the text rather than enlarging it.

**5. A ring reads as a frame only with three cues, and the rim must be EVEN.**
- A flat, uniformly saturated blue ring reads as a **browser focus indicator**, not a frame.
- Supply all three: a directional gradient (lit to one side), a **hairline gap** between portrait and
  ring, and a soft outer glow.
- **Build the rim by blending toward a CONSTANT colour, never by adding light.** An additive rim
  brightens whatever is underneath, so it blows out over a light background and barely shows over a
  black garment (measured spread 24.0 additive vs 17.0 constant, same geometry). Blending toward a
  fixed colour lands the same over both.
- Keep the ring **inside** the crop radius or the crop truncates it.
- **PIL trap:** carving a gap with `draw.ellipse(..., fill=...)` paints a solid disc **over the
  subject** — a separator is `outline=` or a masked-layer annulus, never a `fill=`.

**6. Identity — never generate a likeness from a text description.** Build from the existing base
render with img2img so the face IS the source image, and re-render from the base each time. After
every edit, **look at the output AND measure a face-region mean luminance** before showing the user
(a wiped face reads ~10; a real one >60). A compositing bug that erased the portrait was caught only
by the number, not by reading the code.

Construction recipes — circle-safe geometry, the even-rim maths, the dark/light composite test:
`references/avatar-construction.md`.

## Pitfalls

- **cairosvg must be installed:** `pip install cairosvg` — not always available
- **SVG text rendering varies** — fonts may not render in cairosvg. Use system fonts or embed paths.
- **PNG export quality** — set `output_width` to 2× target for retina (e.g., 2400 for 1200px display)
- **File naming** — use kebab-case: `gentech-avatar.svg`, not `Gentech Avatar.svg`
- **SVG as .png** — don't write SVG content to a .png path. Write .svg first, then convert.

## Deploy Pattern
```bash
# Create asset
write_file('assets/brand-asset.svg', svg_content)

# Convert to PNG
python3 -c "
import cairosvg
cairosvg.svg2png(url='assets/brand-asset.svg', write_to='assets/brand-asset.png', output_width=1200)
"

# Deploy
git add assets/brand-asset.* && git commit -m "feat: brand asset name" && git push
```

## AI-Generated Logos (Alternative Workflow)

For hackathon submissions or quick iteration, use BlockRun image generation instead of SVG:

```python
# Icon mark (avatar, social)
blockrun_image(
    prompt="Minimalist tech logo for PROJECT NAME — dark background (#0a0a0f), accent color. Clean geometric icon. No text.",
    model="openai/gpt-image-2",
    size="1024x1024"
)

# Banner (GitHub header)
blockrun_image(
    prompt="Professional logo with text 'PROJECT NAME' for GitHub repo header. Dark background. Accent color icon + white text + tagline. Horizontal banner.",
    model="openai/gpt-image-2",
    size="1536x1024"
)
```

**When to use AI vs SVG:**
| Need | Use | Why |
|------|-----|-----|
| Hackathon logo (quick) | BlockRun AI | 2 min, no design skills needed |
| Brand identity (durable) | SVG | Editable, scalable, source of truth |
| Social media assets | SVG | Consistent sizing, pixel-perfect |
| Repo thumbnail | Either | AI for speed, SVG for control |

**Pitfall:** AI-generated logos are raster (PNG), not vector. Fine for web/social but not for print or extreme scaling.

⚠️ **When the `blockrun_image` MCP tool is not loaded in the session, do not give up on AI generation** —
call the SDK directly. See `blockrun-wallet-management/references/direct-sdk-invocation.md` for the
`ImageClient` img2img call, the funded-key rule (two `.session` files can hold different keys), and the
`data:`-URI response handling. **Measure the charge as a wallet balance delta** — `cost_usd` has come
back `undefined` on successful runs.

**Token image iteration pattern (proven Jul 22, 2026 for $TREASURY):**
1. Generate a concept (vault + G logo, gold/blue palette)
2. Get feedback from a strong model (Kimi K3 provided useful critique: too busy, G logo needs context, text illegible at small sizes)
3. Simplify based on critique — remove text, reduce clutter, one focal point
4. Test at small sizes (wallet icon, DEX screener thumbnail)
5. The final design was the simplest — just the gold vault door with G logo on dark navy, no text, no building
6. **Key lesson:** Token images display at 32-64px on DEX screeners. If it doesn't read at that size, it's too complex.
7. **For token images specifically:** Use BlockRun image generation (openai/gpt-image-2) for the concept, then iterate with reference_image_urls for style consistency. The `image_generate` tool with `reference_image_urls` preserves the vault/treasury aesthetic while changing composition.
8. **Kimi K3 is a strong design critic** — when iterating on token/logo images, run the concept through Kimi K3 for honest feedback before finalizing. It catches generic AI-art problems (too busy, incoherent lettering, text that won't render at small sizes).

## Proven Use Cases
| Asset | Date | Notes |
|-------|------|-------|
| Seed mark logo | Jun 14, 2026 | Root + circuit gradient |
| X/Twitter avatar | Jun 14, 2026 | Synthwave character |
| X/Twitter banner | Jun 14, 2026 | Seed + pillars + chains |
| GitHub thumbnail | Jun 14, 2026 | Agents + charts + blueprints |
| Previous thumbnail | Jun 14, 2026 | "AAE/AI" positioning (replaced) |
| CMC Strategy Engine logo | Jun 20, 2026 | BlockRun AI gen — icon + banner for BNB Hack |
