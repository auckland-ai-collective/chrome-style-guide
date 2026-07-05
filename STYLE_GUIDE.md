# Application Chrome — Implementation Spec

Implementation spec for the Electron window shell (title bar, tab strip, toolbar,
omnibar, menus, overlays). This is the **authoritative text spec**. The rendered
reference lives in `Chrome Style Guide.dc.html` (annotated) and
`Chrome Style Guide Window.dc.html` (interactive window). When a number here and a
number in the HTML disagree, the HTML is the source of truth — update this file to match.

- **Platform:** Windows-style window controls (min / max / close on the title bar).
- **Theme:** Dark only.
- **Font:** `'Segoe UI', system-ui, -apple-system, sans-serif` — no webfont. Matches native rendering.
- **Design origin:** Original design using browser-derived interaction patterns. Not a clone.

---

## 1. Design tokens

### Surfaces (top → bottom of the window)
| Token | Hex | Used for |
|---|---|---|
| `--title-bar` | `#171717` | Title bar (app identity + window controls) |
| `--tab-strip` | `#292a2d` | Inactive tab track |
| `--chrome` | `#35363a` | Toolbar surface **and** the active tab (they read as connected) |
| `--canvas` | `#202124` | Omnibar field + page content area |
| `--callout` | `#2a2b2e` | Inset info panels / callouts |
| `--control-hover` | `#3c4043` | Icon-button hover, chevron button rest, highlighted menu row |
| `--control-hover-2` | `#484c50` | Secondary hover (stepper, highlighted row hover) |
| `--divider` | `#3c4043` | Hairlines and separators |
| `--menu` | `#232427` | Menu panel background |
| `--menu-footer` | `#1b1c1e` | App-menu footer strip |

### Text
| Token | Hex | Used for |
|---|---|---|
| `--text-primary` | `#e8eaed` | Headings, active labels |
| `--text-secondary` | `#bdc1c6` | Body copy, inactive tab label |
| `--text-tertiary` | `#9aa0a6` | Meta, captions, menu shortcuts |
| `--text-dim` | `#80868b` | Omnibar placeholder (intentionally dim, non-distracting) |
| `--text-disabled` | `#75797e` | Disabled nav icon / disabled menu item glyph |

### Accent & status
| Token | Hex | Used for |
|---|---|---|
| `--accent` | `#2f6fd6` | Primary button, focus ring, active states, promo banner |
| `--link` | `#8ab4f8` | Inline links |
| `--close-red` | `#e81123` | Window close-button hover fill |
| `--favicon-neutral` | `#5f6368` | Placeholder favicon tile |

### Radii
- Tab: `10px 10px 0 0` · Omnibar/pills: `full` (17–18px on 34px height) · Icon buttons: `8px`
- Menu panel: `12px` (app) / `10px` (context) · Menu rows highlighted: `10px` · Cards/callouts: `12px`

---

## 2. Typography
Single family (see above). Sizes / weights:

| Role | Size / weight | Notes |
|---|---|---|
| Content headline | 26 / 400 | Light weight, `letter-spacing:.2px` |
| Content body | 15 / 400, line-height 1.6 | Column headings reuse 15/600 |
| Button label | 13 / 600 | |
| Omnibar placeholder | 14 / 400 | color `--text-dim` |
| Tab title / title-bar text | 12 / 400 | |
| Menu item label | 14 / 400 | |
| Menu shortcut | 13 / 400 | color `--text-tertiary` |
| Popup section label | 12 / 600, `letter-spacing:.4px` | e.g. "Open Tabs" |

---

## 3. Title bar  (Electron-specific — a browser lacks this)
Carries app identity + OS window controls so the tab strip stays dedicated to tabs.

- **Height:** 36px · **Background:** `#171717`
- **Draggable region:** `-webkit-app-region: drag` on the whole bar **except** the window controls (`no-drag`).
- **Left cluster:** 15px app mark (4px radius, accent fill) · 9px gap · app name 12px `#c8ccd0` · optional em-dash subtitle `#6b7075`.
- **Window controls (right):** three cells, each **46 × 36px**, glyph 12px / 1px stroke.
  - Minimize glyph: bottom line. Maximize: 7px square. Close: X.
  - Min/Max hover fill `#2a2a2c`. **Close hover fill `#e81123`, glyph turns `#fff`.**

---

## 4. Tab strip
Left → right: **search chevron**, scrollable **tab track**, **new-tab (+)** button.
The + sits immediately after the rightmost tab (the track is `flex:0 1 auto`, NOT
`flex:1` — do not let it stretch and push + to the far edge).

- **Strip height:** 40px · background `#292a2d` · `align-items:flex-end` · padding `0 6px 0 4px`.

### Shared centerline (critical)
Every interactive element on the strip must share **one vertical centerline at 23px
from the strip top**. Because tabs are 34px tall pinned to the bottom of the 40px
strip, their content centers at 23px; the chevron and + button must be nudged down
(reduced bottom margin) so their centers also land at 23px. Verified: chevron,
favicon, tab label, close button, and + all share the same center.

### Search chevron (tab-search dropdown trigger)
- **32 × 30px** (near-square — do NOT make it wide/short), radius 8px.
- **Rest fill `#3c4043`** (it must read as a real button, not a bare icon), hover `#4a4d51`.
- Glyph: 16px downward chevron, `#e8eaed`. Margin `0 6px 2px 2px`.

### Tabs
- **Height:** 34px · radius `10px 10px 0 0` · padding `0 10px` · icon↔label gap 8px.
- **Max width 240px, min width 52px.** Width = `min(240, trackWidth / tabCount)`.
- **Favicon:** 16 × 16, radius 4px, neutral `#5f6368` tile.
- **Close button:** **24 × 24**, radius 6px, glyph 13px. Transparent at rest; hover fill `#5f6368`, glyph `#fff`.
- **Active tab:** fill `#35363a` (matches toolbar). **Hover (inactive):** +6% white overlay. **Inactive:** transparent, label `#bdc1c6`.
- **Label collapse:** below ~90px width the label + close button drop out, leaving only the favicon. The **active tab always keeps its label and close button.**

### New-tab (+) button
- **30 × 30px**, radius 8px, transparent rest / hover `#3c4043`, 16px + glyph. Margin `0 2px 2px 4px`.

### Shrink & scroll behavior
Tabs shrink evenly from 240px as tabs are added. At the 52px minimum they stop
shrinking; the strip then becomes horizontally scrollable and the **mouse wheel
scrolls it left/right**.

---

## 5. Toolbar & omnibar
- **Toolbar height:** 48px · background `#35363a` · padding `0 8px` · gap 2px.
- **Nav buttons (back / forward / reload):** 32px circles, glyph 17–18px / 2px stroke, hover `#4a4d51`. Often suppressed in app mode. **Disabled** (e.g. no forward history) → glyph `#75797e`.
- **Omnibar:** fills the track. Height 34px, radius 18px (pill), fill `#202124`, padding `0 16px`, icon↔text gap 12px.
  - Leading **search icon** 16px `#9aa0a6`.
  - Placeholder 14px `--text-dim` (`#80868b`) — deliberately dim so it isn't distracting.
- **Right cluster (optional — usually suppressed):**
  - Secondary pill (e.g. "Private"): height 34px, radius 17px, fill `#3c4043`, 16px icon + 13px label.
  - **Primary split button** (e.g. "Finish setup"): fill `--accent`, `#fff` text 13/600, with a 34 × 34px kebab cell on the right; kebab hover `rgba(0,0,0,.15)`.

---

## 6. Tab-search popup
Opened by the chevron or `Ctrl+Shift+A`. Anchored **top-left** under the chevron
(`top:42px; left:6px`). Dismiss on outside click or Esc.

- **Panel:** width 480px, radius 12px, fill `#292a2d`, shadow `0 16px 44px rgba(0,0,0,.55)` + 1px light hairline.
- **Search input row:** padding `14px 18px`, 18px search icon, 15px input text, shortcut hint 12px `#80868b` on the right. Divider below.
- **Section label:** 12/600 `#9aa0a6`, `letter-spacing:.4px` (e.g. "Open Tabs", then "Recently Closed").
- **Row:** height ~54px, padding `9px 10px`, radius 8px, hover / selected fill `#3c4043`.
  - 36 × 36 favicon tile (radius 8px) · title 14/600 `#e8eaed` · meta 12px `#9aa0a6` (`url • time`, both truncate).
  - **Close button appears on row hover:** 26px circle, hover fill `#5f6368` / `#fff`.
- Empty state: centered "No matching tabs" 13px `#80868b`.

---

## 7. Menus (app menu + context menu)
Both share one language: `#232427` panel, dismiss on outside click / Esc.

### Shared item spec
- Row height **40px** (app menu) / **34px** (context menu). Padding `0 16px` (app) / `0 12px` (context); label↔icon gap 18px (app).
- Leading icon 18px `#c8ccd0`. Label 14px (app) / 13px (context). Shortcut 13px `#9aa0a6`, right-aligned.
- **Hover fill:** `rgba(255,255,255,.06)`.
- **Submenu:** trailing 16px chevron `#9aa0a6`.
- **Disabled item:** opacity `0.45`.
- **Divider:** 1px `#3c4043`, 6px vertical margin (context dividers inset 8px horizontally).

### App menu (from the kebab, top-right)
- Width **340px**, radius 12px, shadow `0 18px 50px rgba(0,0,0,.6)`. Scrolls if taller than viewport.
- **Promo banner** (optional, e.g. "Relaunch to update"): accent fill, radius 10px, 8px inset from panel edges, 20px icon + 14/600 white title + secondary caption.
- **Highlighted row** (e.g. "Private"): fill `#3c4043`, radius 10px, 8px horizontal inset, 28px circular leading avatar, trailing submenu chevron; hover `#484c50`.
- **Zoom stepper row:** 48px tall. Two 34px circular −/+ buttons (fill `#3c4043`, hover `#484c50`) flanking a 100% label, then a 1px divider and a 34px fullscreen button.
- **Footer strip:** fill `#1b1c1e`, top border `#3c4043`, 52px tall, 18px icon + 13px `#9aa0a6` text (e.g. profile / org line).

### Context menu (right-click on canvas)
- Width **224px**, radius 10px, shadow `0 16px 44px rgba(0,0,0,.55)` + hairline. Compact 34px rows.
- Anchors at the cursor, clamped inside the window (≥ `width-236` / `height-300`) so it never overflows the frame.
- Typical items: Back (disabled if no history) · Forward · Reload `Ctrl+R` · — · Save as… `Ctrl+S` · Print… `Ctrl+P` · — · View page source `Ctrl+U` · Inspect.

---

## 8. Content patterns
In-app pages center on an **820px** column.

- **Circled framework icon:** 112px circle, fill `#dadce0`, 52px glyph at 1.6 stroke, `#202124`.
  - Must be `flex:0 0 auto` inside a flex column, or the column will squash it into an oval.
- **Empty / informational state:** circled icon → light headline (26/400) → body paragraph (15/400, 1.6, `#bdc1c6`) → optional two-column fact split → callout.
- **Callout:** fill `#2a2b2e`, radius 12px, padding `20px 22px`. 20px leading icon + 15/600 title, then 14/1.6 body with inline `--link` action.

---

## 9. Behavior rules summary
- Only one overlay open at a time (chevron popup, app menu, context menu mutually close each other).
- All overlays dismiss on outside click; the context menu also dismisses on a second right-click.
- Tab close: if the active tab is closed, activate the neighbor at the same index (clamped).
- Keep the shared 23px tab-strip centerline whenever adding/altering strip controls.
- Omnibar placeholder always uses `--text-dim`; never brighten it.
