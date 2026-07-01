# Memory Match

A **card-matching memory game** built with vanilla HTML, CSS 3D transforms, and JavaScript. Features two difficulty modes, a real-time timer, and persistent best-score tracking — all running in the browser with no dependencies.

## Live Demo

[Play Now](https://sneha-chandra.github.io/memory-match-game) &nbsp;·&nbsp; [GitHub](https://github.com/Sneha-chandra/memory-match-game)

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                      Browser Runtime                     │
│                                                          │
│   ┌──────────────────────────────────────────────────┐  │
│   │                  Game Configuration               │  │
│   │                                                   │  │
│   │  EMOJIS[24]  ← emoji pool for card faces         │  │
│   │  MODES = { easy: 4×4, hard: 6×6 }               │  │
│   │  Current Mode → grid dimensions + card count     │  │
│   └──────────────────────┬───────────────────────────┘  │
│                           │                              │
│   ┌───────────────────────▼───────────────────────────┐ │
│   │               Game State Manager                  │ │
│   │                                                   │ │
│   │  cards[]         ← shuffled emoji pairs          │ │
│   │  flipped[]       ← indices of face-up cards      │ │
│   │  matched Set     ← indices of matched pairs      │ │
│   │  moves           ← move counter                  │ │
│   │  timerInterval   ← setInterval handle            │ │
│   │  seconds         ← elapsed time                  │ │
│   │  locked           ← block input during animation │ │
│   └──────────────────────┬───────────────────────────┘ │
│                           │                              │
│   ┌───────────────────────▼───────────────────────────┐ │
│   │               Card Flip Engine                    │ │
│   │                                                   │ │
│   │  flipCard(i)     ← toggle CSS class .flipped     │ │
│   │  checkMatch()    ← compare flipped[0] vs [1]     │ │
│   │  On match:  add to matched, check win condition  │ │
│   │  No match:  wait 800ms → flip both back          │ │
│   │  lock flag: prevents third card click mid-reveal │ │
│   └──────────────────────┬───────────────────────────┘ │
│                           │                              │
│   ┌───────────────────────▼───────────────────────────┐ │
│   │                CSS 3D Renderer                    │ │
│   │                                                   │ │
│   │  .card → perspective wrapper                     │ │
│   │  .card-inner → rotateY(180deg) transition        │ │
│   │  .card-front → face-down (? icon)               │ │
│   │  .card-back  → emoji face, backface-hidden       │ │
│   │  .card.flipped → triggers transform              │ │
│   │  .card.matched → green glow, scale(1.05)        │ │
│   └──────────────────────┬───────────────────────────┘ │
│                           │                              │
│   ┌───────────────────────▼───────────────────────────┐ │
│   │              Persistence Layer                    │ │
│   │  localStorage["bestMoves-easy"]  → int           │ │
│   │  localStorage["bestMoves-hard"]  → int           │ │
│   └───────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## Project File Structure

```
memory-match-game/
├── index.html          # Complete game — HTML structure, CSS styles, JavaScript logic
└── README.md
```

---

## Technical Knowledge Implemented

### CSS 3D Card Flip Mechanism

The flip effect uses the CSS 3D transform pipeline:

```css
/* Container provides depth context */
.card { perspective: 600px; }

/* Inner div is what rotates */
.card-inner {
  transform-style: preserve-3d;
  transition: transform 0.4s ease;
}

/* When flipped class is added */
.card.flipped .card-inner {
  transform: rotateY(180deg);
}

/* Front stays face-down; back reveals emoji */
.card-front  { backface-visibility: hidden; }
.card-back   { backface-visibility: hidden; transform: rotateY(180deg); }
```

This creates a genuine 3D flip where the front and back are two faces of the same card.

### JavaScript Concepts
| Concept | Implementation |
|---|---|
| **Fisher-Yates Shuffle** | `shuffle(arr)` — `for i from n-1 to 1: swap arr[i] with arr[random(0..i)]` |
| **Set for matched tracking** | `O(1)` lookup whether card index is already matched |
| **Input locking** | `locked` flag prevents clicking a 3rd card while 2 are face-up |
| **setTimeout coordination** | 800ms delay before flipping unmatched cards back |
| **Timer** | `setInterval(1000)` increments `seconds`; `clearInterval` on win/reset |
| **Closure over game state** | `flipCard(i)` captures current `flipped[]` and `matched` via closure |
| **Dynamic grid generation** | Cards built with `document.createElement()` and appended to grid |

### Game Logic Flow

```
startGame()
    │
    ├── Pick EMOJIS (n/2 pairs, shuffle)
    ├── Render grid of n card divs
    ├── Reset state: moves=0, seconds=0, matched={}
    └── Start timer

On card click:
    flipCard(i)
        │
        ├── If already flipped or matched → ignore
        ├── Add CSS .flipped class → triggers CSS 3D rotation
        ├── Push to flipped[]
        │
        └── If flipped.length === 2:
               lock = true
               checkMatch()
                   │
                   ├── Match → add both to matched, lock=false
                   │   └── matched.size === total pairs → showWin()
                   │
                   └── No match → setTimeout(800ms):
                           remove .flipped from both, lock=false
```

### Difficulty Modes

| Mode | Grid | Cards | Emoji Pairs | Best Score Key |
|---|---|---|---|---|
| Easy | 4×4 | 16 | 8 pairs | `bestMoves-easy` |
| Hard | 6×6 | 36 | 18 pairs | `bestMoves-hard` |

---

## How to Run

```bash
open index.html
# or
python3 -m http.server 8080
```

---

## Controls

| Action | Input |
|---|---|
| Flip card | Click / Tap card |
| New game | Click "New Game" button |
| Switch mode | Click Easy / Hard button |

---

## Skills Demonstrated

`CSS 3D Transforms` · `backface-visibility` · `Fisher-Yates Shuffle` · `Set Data Structure` · `DOM Generation` · `Input Locking Pattern` · `Timer with setInterval` · `localStorage Persistence` · `CSS Animation` · `Event Handling` · `Vanilla JavaScript (ES6+)`
