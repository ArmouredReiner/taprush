# LevelRing ⚡

A retro arcade timing game built with vanilla HTML, CSS, and JavaScript.

## Gameplay

**Objective:** Tap when the green dot passes through the red zones on the ring.

- The green indicator orbits the ring continuously
- Red zones appear at random positions (stationary)
- Tap when the green dot is inside a red zone to score
- Miss the zone (dot exits without tapping) = Game Over
- Complete all required hits to advance to the next level

## Levels

| Level | Difficulty | Speed | Hits Required |
|-------|-------------|-------|---------------|
| 1     | EASY        | 1.8°/frame | 5         |
| 2     | MEDIUM      | 2.5°/frame | 7         |
| 3     | HARD        | 3.4°/frame | 9         |
| 4     | EXPERT      | 4.5°/frame | 11        |
| 5     | MASTER      | 5.8°/frame | 13        |

## Features

- **5 difficulty levels** with increasing ring speed
- **Random zone placement** — red zones spawn at random positions (minimum 80° from indicator)
- **Score system** — earn points per hit, with speed bonus
- **Leaderboard** — top 10 local scores saved to localStorage
- **Dark/Light mode** toggle
- **CRT aesthetic** — scanline overlay, neon glow effects
- **Cyberpunk city background** — animated parallax cityscape with neon lights, rain, and scrolling building layers
- **Sound effects** — Web Audio API for hit/miss/level-up sounds
- **Haptic feedback** — Vibration API support
- **Mobile-first** — optimized for touch devices

## Tech Stack

- **HTML5** — single-file game structure
- **CSS3** — custom properties, animations, CRT effects
- **Vanilla JavaScript** — no frameworks, requestAnimationFrame game loop
- **Press Start 2P font** — Google Fonts retro pixel font
- **Vercel** — deployment and hosting

## Running Locally

Simply open `index.html` in any modern browser:

```bash
# Or use a local server
python -m http.server 8000
# Then visit http://localhost:8000
```

## Live Demo

**Production URL:** https://taprush-one.vercel.app

## Repository

https://github.com/ArmouredReiner/taprush

## License

MIT