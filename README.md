# Truth Royale Battle

A single-file, browser-based real-time strategy battler inspired by Clash Royale — deploy troops, buildings, and spells with Elixir to destroy your opponent's towers. Built entirely in vanilla HTML, CSS, and Canvas 2D JavaScript, with a custom AAA-style animated SVG logo.

**Theme:** Truth vs. Lie, Light vs. Dark, Reality vs. Illusion. Card design and flavor text are built around concepts of truth, deception, and persuasion rather than generic fantasy tropes.

## Play

Open `truth_royale_battle.html` directly in any modern browser. No build step, no server, no dependencies to install.

```bash
open truth_royale_battle.html        # macOS
xdg-open truth_royale_battle.html    # Linux
start truth_royale_battle.html       # Windows
```

It also works as a static file served from any host (GitHub Pages, Netlify, S3, etc.) — just upload the single HTML file.

## Requirements

- A modern browser with Canvas 2D and SVG filter support (Chrome, Edge, Firefox, Safari)
- Internet access on first load, to fetch:
  - Tailwind CSS (`cdn.tailwindcss.com`)
  - Google Fonts: Orbitron, Rajdhani, Cinzel
- No npm, no build tools, no backend

## How to Play

1. **Main Menu** — tap **⚔️ START BATTLE** to build a deck, or **📋 VIEW CARDS** to browse all available cards.
2. **Deck Builder** — select exactly 8 cards from the full roster, then start the match.
3. **Battle** — drag/tap your side of the arena to deploy cards as your Elixir bar fills. Destroy enemy towers to take crowns:
   - Knock out a **Princess Tower** (side lane) for 1 crown.
   - Knock out the **King Tower** for the win (and remaining crowns).
4. **Match length** is 3 minutes, with a 1-minute sudden-death overtime if it's a draw.

## Game Configuration

Core balance constants live in `CONFIG` near the top of the script:

| Constant | Value | Meaning |
|---|---|---|
| `ELIXIR_MAX` | 10 | Elixir cap |
| `ELIXIR_REGEN` | 0.35/s | Elixir regen rate |
| `BATTLE_TIME` | 180s | Regulation match length |
| `OVERTIME_TIME` | 60s | Sudden-death length |
| `TOWER_KING_HP` | 4000 | King tower health |
| `TOWER_PRINCESS_HP` | 2400 | Side tower health |
| `TOWER_KING_DAMAGE` / `TOWER_PRINCESS_DAMAGE` | 100 / 60 | Tower attack damage |
| `TOWER_RANGE` / `TOWER_ATTACK_SPEED` | 80 / 0.8 | Tower range & attack speed |

## Card Roster

Defined in the `ALL_CARDS` array — 20 cards across three types:

- **13 Troops** — e.g. Truth Guardian, Light Seeker, Whistleblower, Fact Checker, Investigator, Philosopher (truth-aligned) vs. Propagandist, Deceiver, Manipulator, Distraction, Decoy, Conformist, Selective Perceiver (deception-aligned)
- **2 Buildings** — Beacon of Truth, Echo Chamber
- **5 Spells** — Revelation, Confusion, Silencing, Whirlwind of Lies, Forgetfulness

Each card has `hp`, `damage`, `attackSpeed`, `moveSpeed`, `range`, `target`, a unique `special` mechanic, flavor `desc`, and a `color` used to generate its portrait.

Portraits aren't static images — they're procedurally painted onto an offscreen canvas per card (see `PORTRAIT_PAINTERS`, `bgFrame`, `neonGlowStroke`, `particleField`, `rarityFrame`) and cached as bitmaps, so there are no external art assets to manage.

## Architecture

Everything lives in one file: `truth_royale_battle.html`.

```
<head>
  Tailwind CDN + Google Fonts
  <style> — all game UI, HUD, and logo CSS/animations

<body>
  #game-container
    <canvas>               — battle rendering (arena, units, towers, effects)
    .ui-overlay             — HP bars, crowns, timer, elixir bar, card deck
    #start-screen            — main menu (now featuring the SVG logo, see below)
    #deck-screen              — deck builder grid
    #cards-screen              — full card browser
    #result-screen              — win/lose/draw summary

  <script>
    CONFIG, ALL_CARDS                — data
    Portrait engine                  — procedural card art
    class Game { ... }               — game loop, input, rendering, combat, UI state
    const game = new Game()          — boots the app
```

There's no module system or bundler — everything is global script in execution order, which is intentional for a single-file deliverable.

## Logo

The start screen features a custom-built, animated SVG emblem (no external image assets) replacing the original plain-text title:

- **Metallic 3D typography** — `TRUTH` / `ROYALE` / `BATTLE` rendered with linear gradients, embossed strokes, and layered glow filters; gold serif (Cinzel) for `ROYALE`, futuristic (Orbitron) for `TRUTH`/`BATTLE`
- **Glowing crown** with a pulsing gold glow above the wordmark
- **Energy wings** sweeping out behind the text (blue-left, purple-right radial gradients)
- **Dual-direction rotating rune circles** (one clockwise, one counter-clockwise, different speeds)
- **Floating energy shards**, ambient drifting particles, and flickering lightning bolts
- **Per-word shine sweep** animation, staggered across the three lines
- **Pulsing background aura**

All effects are pure CSS `@keyframes` driving SVG/CSS transforms and opacity — no JS, no canvas, no image requests. `prefers-reduced-motion: reduce` disables all animation for accessibility. The logo container is responsive (`width:min(86vw,420px)`) and tested down to 375px-wide mobile viewports.

To restyle or swap the logo, look for the `.trb-*` CSS classes and the `#trbLogo` SVG block inside `#start-screen`.

## Known Limitations

- Single-player only; there is no real opponent AI matchmaking — check the `Game` class for how the opposing side behaves.
- All assets (fonts, Tailwind) load from public CDNs; for fully offline use, vendor them locally and update the `<link>`/`@import` references.
- No persistence — deck selection and match state reset on reload.

## License

Add your preferred license here (e.g. MIT) before publishing.
