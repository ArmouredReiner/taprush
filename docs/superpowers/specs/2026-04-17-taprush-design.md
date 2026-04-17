# TapRush ⚡ — Retro Arcade Reaction Game

## 1. Concept & Vision

TapRush is a mobile-first reaction speed tester wrapped in a **retro arcade cabinet aesthetic**. Think neon-lit CRT screens, glowing phosphor colors, and chunky pixel-style typography — but running on a modern smartphone. The game should feel like discovering a hidden arcade machine from 1987 that somehow knows your name and high score. Fast, addictive, and satisfying with every tap.

---

## 2. Design Language

### Aesthetic Direction
**Reference:** 80s arcade cabinets (Pac-Man, Space Invaders) crossed with synthwave album art. Dark backgrounds with neon glow effects, scanline overlays, and chunky display fonts that feel like LED segment displays.

### Color Palette
| Role        | Color     | Hex       |
|-------------|-----------|-----------|
| Background  | Deep Black | `#0a0a0f` |
| Primary     | Neon Cyan  | `#00f5ff` |
| Accent      | Hot Magenta | `#ff00aa` |
| Success GO  | Electric Green | `#00ff88` |
| Warning WAIT| Neon Red   | `#ff3366` |
| Text Primary| Bright White  | `#ffffff` |
| Text Muted  | Dim Cyan  | `#4a9aa0` |

### Typography
- **Display Font:** `"Press Start 2P"` (Google Fonts) — used for game title, scores, state labels
- **Fallback:** `monospace`
- No other fonts needed — the retro font carries the entire visual identity

### Spatial System
- Full viewport height game area (100dvh for mobile)
- Centered content with generous breathing room
- Tap zones minimum 44px height (Apple HIG), but we'll go larger (64px+) for the main tap area
- 8px base spacing unit

### Motion Philosophy
- **State transitions:** Fast flash/scale pulse when color changes (200ms)
- **Score reveals:** Typewriter-style number count-up effect
- **Idle state:** Subtle pulsing glow on the main tap area to invite interaction
- **Game over:** Screen shake on "Too Soon!" using CSS keyframes
- All easing: `ease-in-out` or custom `cubic-bezier(0.4, 0, 0.2, 1)` for snappy arcade feel

### Visual Assets
- No images — pure CSS effects
- CRT scanline overlay via CSS `repeating-linear-gradient`
- Neon glow via `box-shadow` with multiple spread layers
- Subtle noise texture via CSS `background-image` data URI (optional)
- Inline SVG for the ⚡ lightning bolt in the title

---

## 3. Layout & Structure

### Full-Screen Game Container
```
┌─────────────────────────────────────┐
│          [CRT Overlay]              │
│                                     │
│           ⚡ TAP RUSH               │  ← Title (hidden during gameplay)
│                                     │
│         ┌───────────────┐           │
│         │               │           │
│         │   MAIN TAP    │           │  ← Full-screen tap zone
│         │     AREA      │           │
│         │               │           │
│         └───────────────┘           │
│                                     │
│         ▸ Best: 234 ms               │  ← Best score (always visible)
│                                     │
│         ◉ Round 3 / 5              │  ← Round indicator
└─────────────────────────────────────┘
```

### Screen States

**State 1 — IDLE (Start Screen)**
- Dark background with subtle CRT flicker
- Title "⚡ TAP RUSH" with neon glow, pulsing animation
- Subtitle "TAP TO START" blinking
- Best score displayed below if exists
- Tap anywhere to begin

**State 2 — WAIT (Countdown)**
- Background: `#ff3366` (Neon Red) — "pain" color, don't tap!
- Text: "WAIT..." in large Press Start 2P font
- Subtle pulsing glow on the red screen
- Random delay: 2000–5000ms
- If user taps → immediately go to TOO_SOON state

**State 3 — GO (Reaction Window)**
- Background: `#00ff88` (Electric Green) — GO!
- Text: "TAP!" with flash animation
- Screen flashes bright then settles
- Timer starts the instant this state begins
- First tap stops timer → RESULT state

**State 4 — RESULT (Score Display)**
- Background: `#0a0a0f` (Deep Black)
- Large reaction time displayed: "245 ms"
- If new best: "★ NEW BEST! ★" with celebration animation
- Shows delta from best: "+12 ms slower" or "★ 23 ms faster!"
- "TAP TO PLAY AGAIN" button
- After 3 consecutive rounds: show round summary

**State 5 — TOO SOON (Early Tap Penalty)**
- Background: `#ff3366` with screen shake
- Text: "TOO SOON!" in large red text
- Haptic buzz (3 short vibrations)
- Auto-return to IDLE after 1.5s or on tap

### Multi-Round Structure
- 5 rounds per game session
- Running total/average displayed
- After round 5: full session summary with stats

---

## 4. Features & Interactions

### Core Game Loop
1. User taps to start → WAIT state with random 2–5s delay
2. Screen turns green → GO state, timer starts on color change
3. User taps → timer stops, RESULT state shows reaction time
4. After 3 rounds, show summary → back to IDLE or play again

### Local Storage
- `taprush_best`: number (best single reaction time in ms)
- `taprush_leaderboard`: JSON array of top 10 scores with timestamps
- `taprush_stats`: { games_played: number, total_taps: number }

### Vibration API
- "GO" state tap: 50ms vibration
- "TOO SOO"N state: 100ms × 3 pattern with 50ms gaps

### Sound Feedback (Web Audio API)
- **Start tap:** Short blip (880Hz, 50ms)
- **GO state:** Ascending tone sweep (440Hz → 880Hz, 100ms)
- **Result reveal:** Satisfying "ding" (1200Hz, 150ms)
- **Too Soon:** Low buzz (220Hz, 200ms)
- **New record:** Arpeggio flourish

### Leaderboard
- Top 10 local scores stored
- Shows rank, score, and relative date ("2 days ago")
- Accessible from IDLE state via small trophy icon

### Dark Mode
- The game IS dark mode by default
- A light mode toggle in corner (small, unobtrusive)
- Light mode: white background, neon colors adjust for contrast

---

## 5. Component Inventory

### `GameContainer`
- Full viewport wrapper
- CRT scanline overlay (pseudo-element)
- Handles all touch/click events
- States: idle, waiting, ready, result, tooSoon

### `Title`
- "⚡ TAP RUSH" with inline SVG lightning
- Neon glow: `text-shadow: 0 0 10px #00f5ff, 0 0 20px #00f5ff, 0 0 40px #00f5ff`
- Pulse animation in idle state

### `ScoreDisplay`
- Current reaction time in large Press Start 2P
- Count-up animation on reveal (0 → final value over 300ms)
- Unit "ms" in smaller text

### `BestScore`
- Persistent best score with trophy/star icon
- Shows delta: "↑ 12 ms" or "NEW!"
- Positioned at bottom of screen

### `RoundIndicator`
- "Round X / 5" with dot indicators
- Dots fill in as rounds complete
- Subtle, doesn't distract from main action

### `PlayAgainButton`
- Large tap target (full-width, 64px height)
- Neon border with glow on hover/active
- "TAP TO PLAY AGAIN" text

### `LeaderboardPanel`
- Slide-up modal from bottom
- Top 10 scores with rank badges
- Close button (X) top right
- Backdrop blur on background

### `StateMessage`
- Large centered text for each state ("WAIT...", "GO!", "TOO SOON!")
- Appropriate color for each state
- Scale + fade entrance animation

---

## 6. Technical Approach

### Single HTML File Structure
```
index.html
├── <style> — All CSS
├── <main id="game"> — Game container
└── <script> — All JavaScript
```

### CSS Architecture
- CSS custom properties for all colors/timings
- `@keyframes` for all animations (pulse, flash, shake, flicker)
- Media queries: primarily optimized for 375px–428px width (iPhone)
- `dvh` units for reliable mobile viewport

### JavaScript Architecture
```
GameState: { IDLE, WAIT, GO, RESULT, TOO_SOON }
GameData:  { currentState, reactionTime, bestTime, round, scores[], startTime }

Functions:
- initGame()           — Reset state, bind events
- startGame()          — Transition to WAIT
- startWaitingPhase()  — Set random delay, begin countdown
- triggerGo()          — Turn green, start timer
- handleTap()          — Process user tap based on current state
- showResult()         — Display reaction time, check best
- showTooSoon()        — Penalty state with haptic
- playAgain()          — Reset for new round or new game
- vibrate(pattern)     — Wrapper for Vibration API
- playTone(freq, dur)  — Web Audio API sound
- saveScore()          — localStorage persistence
- updateLeaderboard()  — Manage top 10
- renderUI()           — Update DOM based on state
```

### Performance Considerations
- `will-change: transform` on animated elements
- `pointer-events: none` during transitions to prevent double-taps
- Debounce rapid taps (50ms lockout after valid tap)
- Use `requestAnimationFrame` for any continuous animations

### Browser Compatibility
- Modern mobile browsers (Safari iOS 14+, Chrome Android 80+)
- Progressive enhancement: sounds/vibration gracefully absent if unsupported
- localStorage always available in our target browsers
