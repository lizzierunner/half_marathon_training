# 🦸‍♀️ IRON LIZZIE — Half Marathon Training App

> *"She doesn't run FROM the deadline — she runs THROUGH it."*

A fully interactive, comic book–styled progressive web app that turns an 11-week half marathon training plan into an immersive superhero origin story. Built as a **single HTML file** with no frameworks, no build tools, and no dependencies beyond Google Fonts.

**[▶ Live Demo →](https://lizzierunner.github.io/lizzies_training_plan/)**

---

## What It Does

Iron Lizzie is a personal training app for a half marathon on January 9, 2027. Every feature exists because *I actually needed it* while training:

- I run Galloway (run/walk intervals) **and** sometimes steady-state — so both modes are supported
- I run outdoors by default and use the treadmill as a per-workout backup, with auto-generated treadmill steps
- I do compound barbell lifting alongside my running — so full phased strength programming
- I wanted the app on my phone's home screen, not a browser tab — so it's a PWA

---

## Feature Set

### 🏃‍♀️ Training Plan
- **76-day plan** (Oct 26, 2026 – Jan 9, 2027) built on the *Another Mother Runner: Finish It* framework, morphed into Galloway-style intervals
- Runs on Monday, Wednesday, and Friday, with the Friday long run; strength on Tuesday, Wednesday, and Saturday, with a light Wednesday double and Saturday recovery session
- Full 11-week periodization: Base (W1–3) → Build (W4–6) → Peak (W7–9) → Taper (W10) → Race Week (W11)
- Long runs progress 6 → 7 → 8 → 7 → 8 → 9 → 8 → 10 → **11-mile peak** → 8-mile taper → 13.1
- Cutback weeks, strong-finish sessions, and race-effort midweek runs at correct training points

### 💪 Strength Programming
- 32 alternating strength sessions across Tuesday, Wednesday, and Saturday: A/B/A in odd weeks, B/A/B in even weeks; Saturday sessions stay light after the long run
- Strength A (squat/push/shoulder) alternates with Strength B (hinge/pull/hip)
- Every exercise has a **bodyweight alternative** for travel or fatigue days
- Phased correctly: 3×12 base → 4×8 build → 4×5–6 peak → 3×10 light taper → race week activation only

### 🎮 Gamification
- **XP system** — every workout earns XP; 7 ranks from Sidekick to Iron Lizzie
- **Streak tracking** — consecutive days completed with flame indicators
- **9 achievements** — first mission, streak milestones, long run count, halfway point
- **7 milestone unlock panels** — full-screen dramatic comic panels at key moments (first long run, 8 miles, 10 miles, halfway, peak week, race week), each firing with spring animation

### 🧠 ADHD-Friendly Layer
- **Focus mode (default)** — the home screen shows just today's card: one step at a time with a progress bar, ‹ › day navigation, and a "show everything" toggle for the full comic page
- **🚀 Just Start — 5 minutes** — launch checklist → 3-2-1-GO → 5-minute commitment timer with Weezer check-ins (screen stays awake). Finishing earns +5 XP and an easy off-ramp to the minimum mission
- **Loot drops** — completing a mission can drop a Weezer costume (17 to collect, 4 rarities), a Lucky Star (+5–15 XP) or a Critical Hit (+25 XP). Long runs always drop; three dry rolls in a row can't happen
- **Weezer's Wardrobe** — collect and equip costumes; Weezer's speech bubble changes with time of day, streak and progress
- **Forgiving streaks** — rest days count automatically, today stays open until the day ends, and earning a 🛡️ shield (every 5 workouts, max 2) covers a missed day. A broken streak reads "fresh page", not "failed"
- **One-tap debrief** — tap a feeling and you're done; the extra fields are optional behind "add details"
- **Weekly story arcs** — each week has its own villain and run-vibe suggestion to keep things fresh

### 📓 Mission Debrief Journal
- Auto-appears 0.9s after completing any workout
- 5-emoji feel scale (ROUGH → TOUGH → GOOD → STRONG → ON FIRE)
- Optional notes field + actual distance logging
- Past entries shown inline on completed day views
- Journal actual distances feed into the weekly stats miles calculation

### 📊 Weekly Stats Summary
- Miles run vs. planned for the current week (uses journal actual distance when logged)
- XP earned this week
- Completion % bar with flavor text that escalates from *"chapter hasn't been written yet"* to *"UNSTOPPABLE. The villain trembles."*

### 🏋️‍♀️ Treadmill Mode
- Outdoor is the default; treadmill backup choices are saved per workout
- Treadmill mode auto-generates workout steps with 1% incline and mph-based pacing cues
- **Pace table**: expandable 4.5–7.5 mph grid with exact finish times for that day's distance, color-coded by effort zone (easy / moderate / tempo)

### ⏱️ Live Countdown Clock
- Villain panel shows a real-time countdown to race day: `71d 14:22:09` ticking every second via `setInterval`
- Villain name escalates as the race approaches (💀 → 🔥 → ⚠️)

### 📱 Progressive Web App
- `manifest.json` + service worker (cache-first strategy, full offline support)
- Custom Iron Lizzie SVG icons at 192×192 and 512×512
- Install-to-home-screen banner prompt via `beforeinstallprompt`
- Full Apple PWA meta tags — works as a standalone app on iOS Safari

### 🔊 Sound Effects (Web Audio API)
- **Zero external audio files** — all tones generated programmatically via the Web Audio API
- Step checkbox: short ascending ding
- Workout complete: victory chord (C–E–G–C, triangle wave)
- Long run complete: 7-note triumphant fanfare
- 🔊/🔇 toggle in masthead, preference saved to localStorage

### 🗓️ Day Management
- Day tabs scroll horizontally across all 76 days
- **Swap Day** — move any workout to another day within the week, with undo support
- Skip Day — marks a day skipped (orange indicator), no XP penalty
- All state persisted to localStorage across sessions and page refreshes

### ⏲️ Strength Timer
- Built-in interval timer inside each strength workout panel
- Step-by-step navigation with pause / resume / next / reset
- Timer state persisted to localStorage — close mid-workout and return where you left off

---

## Technical Notes

### Why a single HTML file?
This is a **constraint-as-craft** decision. No build toolchain, no `node_modules`, no deployment pipeline — just `index.html`. It made the PWA trivial to cache, forced disciplined CSS architecture using custom properties, and means anyone can inspect the entire source in one place.

### State Management
Current state lives in `localStorage` under the key `ironlizzie_hm_v2`. The previous `ironlizzie_hm_v1` data is left intact; training progress starts fresh while sound, splash, and install preferences are carried forward. Venue choice is saved per workout, with outdoor as the default. The state object tracks:

```js
{
  [date]: { done, skipped, steps, mode, timer, venue },  // per-day workout and venue state
  _swaps: { [date]: swappedDate },
  _sfx: true | false,
  journal: { [date]: { feel, note, actualDist, ts } },
  _achievements: { [id]: true },
  _milestones: { [id]: true },
  _splashSeen: true,
  _installDismissed: true,
}
```

### CSS Architecture
- 12 CSS custom properties for the full color system (`--ink`, `--paper`, `--yellow`, `--red`, `--blue`, etc.)
- Comic book aesthetic: halftone dot patterns via `radial-gradient`, ink-style `box-shadow`, `Bangers` display font
- All animations use `cubic-bezier` spring curves — no jarring linear transitions
- Single responsive breakpoint at 380px

### JavaScript Patterns
- No classes — plain functions and a single global `state` object
- `renderAll()` calls 8 targeted sub-renders on every state change
- Web Audio API used for all sound (no `<audio>` element, no external files)
- `beforeinstallprompt` captured for deferred PWA install UX
- Service worker: cache-first with network fallback and automatic old-cache cleanup

---

## Tech Stack

| Layer | Choice | Why |
|---|---|---|
| Structure | Vanilla HTML | Zero dependencies |
| Styling | Vanilla CSS + custom properties | Full control, no framework overhead |
| Logic | Vanilla JavaScript (ES5-compatible) | No build step, maximum compatibility |
| Fonts | Google Fonts (Bangers, Permanent Marker, Comic Neue) | Comic book character |
| Storage | `localStorage` | Offline-first, no server needed |
| Audio | Web Audio API | Zero files, procedurally generated tones |
| Install | PWA (manifest + service worker) | Home screen app, works offline |

---

## Running Locally

```bash
# Clone the repo
git clone https://github.com/lizzierunner/lizzies_training_plan.git
cd lizzies_training_plan

# Serve locally — service worker requires HTTP, not file://
python3 -m http.server 8080
# or: npx serve .

# Open http://localhost:8080
```

> The app works by opening `index.html` directly in a browser for basic use, but the service worker, PWA install prompt, and offline caching require a local HTTP server or HTTPS host.

---

## The Story Behind It

I'm training for a virtual half marathon on January 9, 2027. I couldn't find a training app that matched how I actually train — Galloway intervals, garage gym compound lifting, switching between outdoor runs and treadmill sessions, tracking how I *feel* not just whether I checked a box.

So I built one. Then I gave it a comic book aesthetic because I like Bangers font and confetti and POW! badges. Then I added XP and ranks because why not. Then it had a service worker. Then the countdown clock was ticking every second.

That's how a training plan becomes a portfolio piece.

---

*Built by Lizzie Johnson · May–August 2026*
