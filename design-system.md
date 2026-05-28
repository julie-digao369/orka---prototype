# Orka 01 Pro — Design System

## 1. Design Tokens

### Colors

| Token | Value | Usage |
|---|---|---|
| `--txt-dark` | `#18212d` | Primary text |
| `--txt-soft` | `#6d7682` | Secondary / muted text |
| `--blue-main` | `#234d77` | Primary brand blue |
| `--blue-deep` | `#102b46` | Deep accent / dark headings |
| `--warm-main` | `#c89e72` | Warm gold accent |
| `--warm-soft` | `#e6d3bf` | Warm tint / subtle highlight |
| `--hero-accent` | `#7d94ab` | Hero decorative accent |
| `--card-bg` | `rgba(247,248,249,0.78)` | Card surface (frosted) |
| `--card-line` | `rgba(255,255,255,0.58)` | Card border highlight |
| `--track-bg` | `#cfd5dc` | Slider track background |
| `--dot-bg` | `#8b9cac` | Inactive dots / indicators |

#### Semantic Colors

| Context | Color | Notes |
|---|---|---|
| Disconnected / Error | `#FF4B3A` | Red LED + tinted chip bg |
| Reconnecting | `#c4a56e` | Gold spinner + button bg |
| Connected / Success | `#5a9e78` | Green icon + button bg |
| Dark UI overlay | `rgba(20,24,33,0.92)` | Toast / tip background |
| Primary button | `#1d1d1f` | Near-black solid |

### Background System

Multi-layer gradient with animated orbs for depth:

```
Base gradient:    180deg — #DDE2EB → #F4F1EC → #F9F6F0
Orb A (cool):    rgba(194,209,235,0.36) — blend-mode: screen
Orb B (warm):    rgba(244,226,198,0.28) — blend-mode: screen
Outer body:      180deg — #d9e0e6 → #edf0f2 → #f1eee8 → #ebe7e0
```

### Typography

**Font Stack:**
```
-apple-system, BlinkMacSystemFont, "SF Pro Display",
"PingFang SC", "Hiragino Sans GB", "Noto Sans CJK SC",
"Microsoft YaHei", sans-serif
```

**Display Font:** `Noto Serif` (weight 700) — used for decorative headings.

| Level | Size | Weight | Tracking | Usage |
|---|---|---|---|---|
| Page title | 22px | 680–700 | −0.02em | Hero headings |
| Section title | 20px | 680–700 | −0.02em | Card / overlay titles |
| Card heading | 17px | 650 | — | Detail card headers |
| Body / label | 14–16px | 500–600 | −0.01em | Buttons, chips, labels |
| Caption | 13px | 500 | — | Tips, metadata |

### Spacing

| Scale | Value | Usage |
|---|---|---|
| xs | 6px | Icon gaps, LED spacing |
| sm | 8px | Chip inner gap |
| md | 12px | Card gaps, section padding |
| lg | 14–16px | Section gaps, content spacing |
| xl | 24px | Page padding horizontal |
| 2xl | 28–36px | Large vertical margins |

### Radii

| Token | Value | Usage |
|---|---|---|
| pill | 18–25px | Chips, buttons, CTAs |
| card | 16–20px | Cards, panels |
| toast | 12px | Toasts, tips |
| small | 4px | Battery, minor elements |
| circle | 50% | LEDs, avatars, round icons |

### Shadows

| Token | Value | Usage |
|---|---|---|
| `--shadow-soft` | `0 10px 28px rgba(17,31,47,0.10)` | Card elevation |
| `--shadow-thumb` | `0 8px 20px rgba(16,34,55,0.18)` | Slider thumbs |
| Phone frame | `0 24px 72px rgba(12,24,39,0.26)` | Outer device shadow |

### Glassmorphism

Used throughout for frosted surfaces:
```css
background: rgba(247,248,249, 0.78);
backdrop-filter: blur(12px) saturate(160%);
-webkit-backdrop-filter: blur(12px) saturate(160%);
border: 0.5px solid rgba(255,255,255,0.58);
```

---

## 2. Component Inventory

### Status Bar
- Fixed top, 15px/600 weight
- Signal, WiFi, Battery icons (pure CSS)

### Hero / Header
- Page title (22px/700), optional subtitle
- Contains device chips

### Chip (Device Indicator)
- `border-radius: 18px`, frosted glass bg
- LED dot (6px circle) — green/red/spinning states
- States: `.connected`, `.disconnected`, `.connecting`

### Card
- `border-radius: 16–20px`, frosted glass
- `--shadow-soft` elevation
- Inner padding ~16px

### Gauge (Volume Arc)
- SVG circular arc with draggable thumb
- `+`/`−` buttons at sides
- Mute pill toggle inside arc
- Supports split L/R mode

### Slider (Horizontal Pill)
- Track: `--track-bg`, 6px height, pill radius
- Thumb: 22px circle, white, `--shadow-thumb`
- Mute button variant to the left

### Tone Card
- Collapsible accordion pattern
- EQ curve visualization (SVG)
- Action buttons row

### Tab Bar
- Floating bottom bar, frosted glass
- 4 tabs with SVG icons
- Active tab: `--blue-main` color + label shift

### Toggle Switch
- 51×31px capsule
- Brand-colored track when on
- Smooth 280ms transition

### Listening Plan Card
- Large 20px radius card
- Icon badge (circular, 42px)
- Title + description layout

### Media Section
- Tab switcher (status/artist/song)
- Media cards with play controls

### Noise Reduction Page
- Mode cards with animated decorative backgrounds
- Landscape/café/office/custom scene icons
- Animated keyframes per mode

### Disconnect Overlay
- Full-screen frosted overlay
- Bluetooth icon (swap on/off SVGs)
- Spinner ring animation
- Primary reconnect button + dismiss link

### Connect CTA
- Pill button with conic-gradient spinning border
- Navy bg `#1B2E4A`

---

## 3. Interaction Patterns

### Transitions
- Default duration: **280ms ease**
- Background theme shifts: **900ms ease**
- Spring-like thumb: **160ms ease** (active scale 0.96–0.97)

### Animations
| Name | Duration | Usage |
|---|---|---|
| `bgFloatA/B` | 42–52s | Ambient orb drift |
| `dcSpin` | 1s linear | Reconnect spinner |
| `ledBlink` | 1.4s ease | Disconnected LED pulse |
| `ledSpin` | 0.8s linear | Connecting LED spin |
| `ringFlow` | 2.4s linear | CTA border gradient rotation |

### Touch Behavior
- `-webkit-tap-highlight-color: transparent`
- `user-scalable=no` viewport
- Active states use `transform: scale(0.96–0.97)`

### Scroll
- `-webkit-overflow-scrolling: touch`
- Overscroll behavior managed per page
- Sticky nav with frosted glass on scroll

---

## 4. Layout Structure

```
.phone (390×844, border-radius 47px)
├── .status (fixed top bar)
├── main.scroll#homePage
│   ├── .hero (header + chips)
│   └── .card × N
├── main.scroll#listeningPage
├── main.scroll#mediaPage
├── main.scroll#noisePage
├── .disconnect-overlay
└── .tabbar (floating bottom)
```

- Grid: `grid-template-rows: 1fr auto`
- Pages swap via `display: none` / visible
- Tab bar persists across pages

---

## 5. Responsive Behavior

Below 500px viewport width → fills full screen, removes phone frame:
```css
@media (max-width: 500px) {
  .phone { width: 100%; height: 100%; border-radius: 0; }
}
```

Safe area insets respected via `viewport-fit=cover` + `env(safe-area-inset-*)`.

---

## 6. Platform Notes

- **PWA-ready**: manifest.json, apple-mobile-web-app-capable
- **iOS Safari optimized**: status bar translucent, touch handling
- **CSS @property** used for animatable custom properties (gradients, angles)
- **No external JS frameworks** — vanilla DOM manipulation
- **Google Fonts**: Noto Serif 700 (preconnected)
