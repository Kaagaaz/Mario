# MARIO ON TEMU

An endless pixel-art platformer that runs entirely in the browser. The whole game is **one `index.html` file**: no build step, no framework, no image files and no audio files. Every sprite and tile is drawn from pixel data in code, and every sound is generated live.

**Play it:** `https://YOUR-USERNAME.github.io/YOUR-REPO/` (replace after you publish, see [Publish on GitHub Pages](#publish-on-github-pages))

---

## Table of contents

1. [Overview](#overview)
2. [Features](#features)
3. [How to play](#how-to-play)
4. [Controls](#controls)
5. [Game rules in detail](#game-rules-in-detail)
6. [Difficulty and speed ramp](#difficulty-and-speed-ramp)
7. [Run it locally](#run-it-locally)
8. [Publish on GitHub Pages](#publish-on-github-pages)
9. [Project structure](#project-structure)
10. [How the code works](#how-the-code-works)
11. [Customize the game](#customize-the-game)
12. [Browser and device support](#browser-and-device-support)
13. [Troubleshooting](#troubleshooting)
14. [FAQ](#faq)
15. [Ideas for future versions](#ideas-for-future-versions)

---

## Overview

MARIO ON TEMU is a side-scrolling platformer in the classic 8-bit style. You run to the right across an endless world of grass, bricks, golden platforms and mystery blocks, collecting coins and stomping enemies. There is no final level. The game starts slow and gentle, then speeds up and gets harder the further you go, until you run out of lives. Your goal is to beat your best score.

It is designed to work equally well on a desktop with a keyboard and on a phone or tablet with on-screen buttons, and it fills the whole screen on both.

## Features

### Gameplay
- **Endless, procedurally generated world.** The level is created ahead of the camera as you run, so no two runs are the same.
- **Slow start, fast finish.** Your top running speed rises with distance, gaps get wider, and enemies get more common and faster.
- **3 lives.** Lose a life by falling into a gap or touching an enemy from the side or below. You respawn on safe ground with a short blink of protection.
- **Stomp enemies.** Land on top of an enemy to squash it for bonus points and a small bounce.
- **Coins and `!` blocks.** Collect coins in the air and on platforms, and hit `!` blocks from below to pop out a coin.
- **Variable jump height.** Hold the jump control for a high jump, tap it for a small hop.
- **Forgiving controls.** A short "coyote time" lets you still jump just after leaving a ledge, and a jump buffer remembers a jump you pressed slightly too early.
- **World counter.** The HUD shows `WORLD 1-1`, which counts up the further you run.
- **Score and best score.** Your best score is saved in your browser and survives page reloads.

### Presentation
- **Pixel art everywhere.** Hero, enemies, coins, blocks, platforms, bushes, clouds, hearts and buttons are all hand-defined pixel graphics. No emojis and no stock images.
- **Pixel font everywhere.** All text uses the Press Start 2P font, including the HUD, buttons and menus, and font sizes are kept to multiples of 8 so text stays sharp.
- **No blurry scaling.** The game draws at a small internal resolution and scales up by a whole number with `image-rendering: pixelated`.
- **Fullscreen layout.** The game fills the whole window, with a fullscreen button in the pause menu.
- **Sound effects** for jumping, coins, stomps and getting hit, generated with the Web Audio API.

### Controls and menus
- **Start pop-up** asking how you want to play:
  - **Keyboard**: shows a short, skippable 3-step tutorial.
  - **On-screen buttons**: skips the tutorial and starts straight away.
- **On-screen controls** with left, right and jump buttons that sit on the brick ground. They support multi-touch, so you can run and jump at the same time.
- **Pause menu** with Resume, Fullscreen and Change Mode.
- **Game over screen** showing your score and best score with a Play Again button.

## How to play

1. Open the game. A pop-up asks how you want to play.
2. Pick **Keyboard** or **On-screen buttons**.
3. Run right. Jump over gaps, onto platforms and on top of enemies.
4. Grab coins and hit `!` blocks from below for extra points.
5. Keep going for as long as you can. When your third life is gone, the game is over and you can try again.

### Tips
- You start slow on purpose. Use the early distance to get a feel for the jump.
- Hold the jump button to clear wide gaps and tall blocks, but tap it for low hops.
- Stomping an enemy gives a bounce. Hold jump as you land to bounce higher.
- When you are fast, start your jump a little earlier than feels natural.
- You cannot run back left past the left edge of the screen, so always keep moving.
- After a respawn you are protected for about two seconds. Use it to get away from nearby danger.

## Controls

| Action | Keyboard | On-screen |
| --- | --- | --- |
| Move left | `A` or `←` | Left arrow button |
| Move right | `D` or `→` | Right arrow button |
| Jump | `Space`, `W` or `↑` | `A` button |
| Pause / resume | `P` or `Esc` | Pause button (top right) |
| Restart after game over | `Enter` or `Space` | Play Again button |

The game also pauses automatically if the browser window loses focus, so you will not die while you are in another tab.

You can switch between keyboard and on-screen controls at any time: pause, choose **Change Mode**, and pick again.

## Game rules in detail

### Lives
You start with 3 lives, shown as hearts in the top right. You lose one when:
- you fall below the ground into a gap, or
- you touch an enemy without landing on top of it.

After losing a life you respawn ahead on the next safe stretch of ground with nearby enemies cleared away, and you are invulnerable to enemy contact for about 2 seconds (the hero blinks). When the last heart is gone, the game ends.

### Scoring

| Event | Points |
| --- | --- |
| Distance travelled | 1 point per tile moved to the right |
| Collect a coin | +10 |
| Hit a `!` block from below | +10 |
| Stomp an enemy | +20 |

Your score and the number of coins collected are shown in the HUD at the top left. Your best score is stored in your browser's `localStorage` under the key `pixelrun_best`.

### World pieces

| Piece | Look | What it does |
| --- | --- | --- |
| Ground | Grass on top of bricks | Solid. Gaps in it are deadly. |
| Golden platform | Cream top with studs | Solid. Jump onto it or bump it from below. |
| `!` block | Purple block with a white `!` | Solid. Hit from below for a coin, then it turns grey and goes empty. |
| Brick pillar | Brown brick blocks, 2 to 3 tall | Solid. Jump over it or onto it. |
| Coin | Tall gold coin | Collect by touching it. |
| Blob | Purple enemy | Walks back and forth and turns around at walls and ledges. Stomp it or avoid it. |
| Bushes and clouds | Green and white shapes | Decoration only, scrolling slower than the ground for a sense of depth. |

### World counter
Every 2400 game pixels of distance counts as one stage. Four stages make one world, so the label goes `1-1`, `1-2`, `1-3`, `1-4`, `2-1`, and so on. It is purely a progress marker and does not change the rules by itself.

## Difficulty and speed ramp

Difficulty depends only on how far you have run. It is a number from 0 (start) to 1 (full difficulty), reached after 9000 game pixels (about 560 tiles) of distance. These are the exact rules in the code:

| Setting | At the start | At full difficulty |
| --- | --- | --- |
| Top running speed | 1.1 pixels per frame | 3.5 pixels per frame (cap reached after about 8640 pixels) |
| Gap width | 2 tiles | up to 4 tiles |
| Chance of an enemy on a long ground section | 25% | 80% |
| Enemy walking speed | 0.35 pixels per frame | 0.75 pixels per frame |
| Second enemy on the same section | Never | 50% chance once difficulty passes 0.5 |
| Tall pillars (3 blocks) | Never | Possible once difficulty passes about 0.35 |

Some details:
- Acceleration is gradual, so you speed up smoothly instead of jumping to top speed.
- Running faster also adds a small bonus to jump strength, so long gaps stay jumpable at high speed.
- The first stretch of ground (about 16 tiles) is always flat and enemy-free, so you can get comfortable.

## Run it locally

The simplest way is to double-click `index.html` and open it in a browser.

To serve it properly from a local web server instead:

```bash
# from the folder that contains index.html
python3 -m http.server 8000
# then open http://localhost:8000
```

Any static file server works (`npx serve`, VS Code Live Server, and so on).

The pixel font is loaded from Google Fonts, so you need an internet connection to see it. Without a connection the game still works, but text falls back to a plain monospace font.

## Publish on GitHub Pages

1. **Create a repository** on GitHub (for example `pixel-run`). Public repositories work with the free plan.
2. **Upload the files.** Add `index.html` and `README.md` to the root of the repository. You can drag and drop them in the GitHub web page, or use git:
   ```bash
   git init
   git add index.html README.md
   git commit -m "Add MARIO ON TEMU"
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO.git
   git push -u origin main
   ```
3. **Turn on Pages.** Go to **Settings → Pages**.
4. Under **Build and deployment**, set **Source** to **Deploy from a branch**, choose the `main` branch and the `/ (root)` folder, then click **Save**.
5. **Wait a minute or two.** Your game will be live at `https://YOUR-USERNAME.github.io/YOUR-REPO/`. The Pages settings screen shows the exact link once it is ready.

Notes:
- The file must be named exactly `index.html` and sit in the root so the site opens without a long path.
- To update the game later, upload a new `index.html` (or push a commit). Pages redeploys automatically. If you do not see changes, do a hard refresh (`Ctrl+Shift+R`, or `Cmd+Shift+R` on a Mac).
- If your repository is named `YOUR-USERNAME.github.io`, the game is served from the root address `https://YOUR-USERNAME.github.io/` instead.
- You can also use a custom domain under **Settings → Pages → Custom domain**.

## Project structure

```
your-repo/
├── index.html   # the whole game: HTML, CSS and JavaScript
└── README.md    # this file
```

Inside `index.html` the code is organised in this order:

| Section | What it contains |
| --- | --- |
| `<style>` | Colors as CSS variables, HUD, buttons, menu panels and on-screen controls. |
| HTML body | The canvas, HUD, hint line, and the start, tutorial, pause, game over and touch-control blocks. |
| Sprites | Pixel art defined as text arrays, converted into small canvases at startup. |
| Tiles | Ground, golden platform, `!` block, empty block and brick, drawn with rectangles. |
| State | Player, enemies, coins, tile map, score, lives. |
| Level generation | `genSeg()` builds the next piece of the world. |
| Collision | Tile-based horizontal and vertical collision for the player and enemies. |
| Audio | Small Web Audio beeps. |
| Game flow | `reset`, `startGame`, `die`, `respawn`, `endGame`, `setPause`. |
| `update()` | One 60 Hz step of movement, physics, enemies, coins and cleanup. |
| Rendering | `resize()` and `draw()`: background, tiles, coins, enemies, hero, HUD text. |
| Input and menus | Keyboard, touch buttons, tutorial steps and menu buttons. |

## How the code works

### Rendering and pixel scaling
The game world is 176 pixels tall and as wide as the window needs. The canvas is created at that small size, then CSS scales it up by a whole number (`round(window height / 176)`) with `image-rendering: pixelated`. Because the scale is always a whole number, every game pixel becomes a clean square of screen pixels, with no blur and no uneven pixels. On resize the scale and the HUD font and button sizes are recalculated.

### Sprites without image files
Each sprite is an array of text rows where every letter stands for a color in a small palette (for example `g` for the cap and `o` for the jacket) and `.` means transparent. At startup these are painted once onto tiny offscreen canvases. Facing left is produced by drawing the same data mirrored, so there is no extra art to maintain. Tiles use the same idea with a few rectangle fills.

### Game loop
The loop uses `requestAnimationFrame` but advances the game in fixed steps of 1/60 second using an accumulator. That keeps movement and difficulty identical on 60 Hz, 120 Hz and slower displays, and avoids huge jumps if the tab was in the background (the time added per frame is capped).

### Level generation
The world is a map of tiles stored by column and row. Each time the camera gets close to the end of the generated area, `genSeg()` adds another section. It picks either a gap (with coins floating over it) or a ground section of 4 to 8 tiles, which may also get:
- a brick pillar (about 22% of the time on longer sections),
- a row of golden platforms and `!` blocks (about 38%),
- a row of coins on the ground (about 18%),
- one or two enemies, depending on difficulty.

Tiles, enemies and coins that are well behind the camera are removed regularly, so memory use stays flat during very long runs.

### Physics and feel
- Horizontal movement uses acceleration and friction instead of instant speed, with extra braking when you reverse direction.
- Gravity is lighter while you hold jump and moving upward, which gives variable jump height.
- Coyote time (6 frames) and a jump buffer (6 frames) make jumps feel fair.
- Collisions are checked per axis against the tile map, which prevents sticking to walls and corners.
- The camera only moves forward and follows the hero at about 35% from the left of the screen, so you can see more of what is ahead.
- The hero's legs only animate while running on the ground. Standing still shows an idle pose, and being airborne shows a jump pose.

### Menus and HUD
The HUD, pause button, menus and on-screen controls are real HTML elements layered over the canvas rather than being drawn into it. This keeps text crisp, makes buttons easy to tap, and lets CSS handle layout. The hearts are drawn from pixel data into a tiny canvas.

### Touch controls
Each on-screen button uses pointer events. Pressing sets a flag that the game loop reads each step. Because every button is independent, you can hold right and tap jump at the same time. Sliding a finger from one button onto another also works.

### Audio
Sounds are short square-wave beeps created with the Web Audio API. Browsers only allow audio after the first user interaction, so the audio context is started when you click one of the start buttons.

## Customize the game

Everything lives in `index.html`. Search for the names below.

| What you want to change | Where to look |
| --- | --- |
| Number of lives | `LIVES` constant near the top of the script |
| Game name shown in the game | `<title>` and the `PIXEL RUN` text in the HUD and start pop-up (these still read "Pixel Run" until you change them) |
| How quickly the game speeds up | `diff()` (distance needed for full difficulty) and `maxRun` in `update()` |
| Top running speed | `maxRun`: the `1.1` (start) and `2.4` (extra speed gained) values |
| Enemy speed | The `e.vx` line in the enemy loop in `update()` |
| Jump strength and gravity | The jump line (`P.vy=-5.3...`) and the gravity line (`0.2` held, `0.5` normal) in `update()` |
| How often enemies, pillars and platforms appear | The probabilities in `genSeg()` |
| Gap sizes | The `w` calculation at the top of `genSeg()` |
| How long you are protected after respawn | `inv:120` in `respawn()` (frames, 60 per second) |
| World counter length | `2400` in `draw()` |
| Colors | Palette objects (`HP`, `EP`), tile drawing code, and the CSS variables in `:root` |
| Hero and enemy art | The `head`, `body`, `legs` and `blobBody` arrays. Keep all rows the same width. |
| Coin art | `coinSpr` |
| Sounds | The `beep(frequency, duration, wave, volume, slide)` calls |
| Tutorial text | The `STEPS` array |

After editing, save the file and refresh the page. There is nothing to compile.

## Browser and device support

- **Desktop:** current versions of Chrome, Edge, Firefox and Safari.
- **Android and iPadOS:** current Chrome, Safari and Firefox. Landscape orientation is recommended.
- **iPhone:** works in Safari. Fullscreen mode is not supported by Safari on iPhone, but the game still fills the browser window. Adding the page to your home screen gives a cleaner view.

The game needs JavaScript, the HTML canvas and (for sound) the Web Audio API.

## Troubleshooting

| Problem | What to try |
| --- | --- |
| Text is not in the pixel font | The font comes from Google Fonts. Check your internet connection and that nothing blocks `fonts.googleapis.com`. |
| No sound | Browsers block audio until you interact with the page. Click a start button. Also check the device is not muted. |
| On-screen buttons do not appear | They only show in **On-screen buttons** mode. Pause and use **Change Mode**. |
| Keyboard does nothing | Click on the page once to give it focus, and make sure the game is not paused. |
| The game paused by itself | It pauses when the window loses focus. Click back into the page and press `P` or the pause button. |
| Fullscreen button does nothing | Some browsers (notably Safari on iPhone) do not allow fullscreen. Use the browser's own fullscreen option or add the page to your home screen. |
| Changes do not show after updating on GitHub | Wait a minute for the deployment, then hard refresh with `Ctrl+Shift+R` (or `Cmd+Shift+R`). |
| Best score disappeared | It is stored in the browser. Clearing site data, private browsing, or a different browser or device starts fresh. |
| The page is blank on GitHub Pages | Make sure the file is named exactly `index.html`, is in the root of the branch you selected, and that Pages is enabled in Settings. |

## FAQ

**Is there a final level or ending?**
No. The world is endless. The challenge is how far you can get and how high you can push your score.

**Why does the game start slowly?**
On purpose. It begins gentle so new players can learn the jump, then ramps up speed and danger with distance.

**Can I play with both keyboard and buttons?**
You choose one mode at the start, and you can change it any time from the pause menu. Keyboard input is always read while the game is running, and the on-screen buttons only appear in button mode.

**Does it save my progress?**
It saves only your best score, in your browser. Each run starts from the beginning.

**Does it need a server or a build tool?**
No. It is a static page. GitHub Pages, Netlify, Vercel or any static host can serve it.

**Can I change the art?**
Yes. The sprites are text arrays in the code, so you can edit them directly. No image editor is needed.

**Does it collect any data?**
No. There are no analytics, no accounts and no network requests except loading the font from Google Fonts. The only thing stored is your best score, on your own device.

## Ideas for future versions

- Power-ups such as a speed boost or a shield.
- More enemy types, for example flying enemies or ones that cannot be stomped.
- Themed worlds with different colors as the world counter increases.
- Background music.
- An online leaderboard.
- Gamepad support.
- A mute button and a volume setting.
- Offline play by hosting the font file in the repository.


### Disclaimer

MARIO ON TEMU is an independent fan-made project. It is not affiliated with, endorsed by, or sponsored by Nintendo or Temu. The names are used only as a joke title, and the game contains no characters, artwork, music or code from any Nintendo game.
