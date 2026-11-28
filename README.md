# Worst Games

Play them at https://worst-games.netlify.app.

- [Baby Toss](baby-toss/): toss baby dolls into trash cans. *Kid Tested, Mother Approved!*

The rest of this page is a tutorial: how Baby Toss was made, so you can change it or make your own game the same
way. It assumes you can code (variables, functions, loops, objects) but have never made a game for the browser.

**Tool for shapes:** the [three.js editor](https://threejs.org/editor/) runs in the browser. Build or import a
model, adjust its shapes until it looks right, then export it as a glTF file and load it into your game.

---

## Contents

1. [Run it on your computer](#1-run-it-on-your-computer)
2. [What a browser game is](#2-what-a-browser-game-is)
3. [Your first game in 100 lines](#3-your-first-game-in-100-lines)
4. [How Baby Toss is laid out](#4-how-baby-toss-is-laid-out)
5. [Drawing: three.js](#5-drawing-threejs)
6. [Physics: cannon.js](#6-physics-cannonjs)
7. [The ragdoll baby](#7-the-ragdoll-baby)
8. [The loop](#8-the-loop)
9. [Input: throwing with a finger or a mouse](#9-input-throwing-with-a-finger-or-a-mouse)
10. [Rules and game state](#10-rules-and-game-state)
11. [Sound without sound files](#11-sound-without-sound-files)
12. [Screens, wording and accessibility](#12-screens-wording-and-accessibility)
13. [Making it run on phones](#13-making-it-run-on-phones)
14. [How it was built: the process](#14-how-it-was-built-the-process)
15. [Recipes: changing Baby Toss](#15-recipes-changing-baby-toss)
16. [Making your own game](#16-making-your-own-game)
17. [Putting it on the website](#17-putting-it-on-the-website)
18. [Where to learn more](#18-where-to-learn-more)

---

## 1. Run it on your computer

Every game is one HTML file. There is no build step and nothing to install.

```sh
git clone https://github.com/blargoflatsome/worst-games.git
cd worst-games
python3 -m http.server 8000      # or: npx serve
```

Open http://localhost:8000/baby-toss/ in a browser. You can also double-click `baby-toss/index.html`, but a local
server behaves more like the real website, so get used to it.

The game loads two libraries from the internet (cdnjs), so you need a connection.

**Your edit loop:** change the file, save, reload the browser. Keep the browser's developer tools open
(F12, or Cmd+Option+I on a Mac). The **Console** tab shows errors, and you can type into it to poke at things while
the game runs.

To try it on your phone while it runs on your computer, use the computer's local IP address instead of
`localhost` (for example `http://192.168.1.20:8000/baby-toss/`), on the same Wi-Fi.

---

## 2. What a browser game is

A browser game is a web page with three parts:

1. **A canvas**: a `<canvas>` element that fills the screen. Everything in the 3D yard is painted onto it.
2. **Some HTML on top**: the menus, the scoreboard and the buttons are ordinary HTML laid over the canvas. They're
   much easier to make look good, and to make work with screen readers, than anything painted on the canvas.
3. **A loop**: a function that runs about 60 times a second. Each time it moves the world forward a little, then
   paints a new picture. Fast enough, and the pictures look like motion.

```
          ┌──────────────────────────────────────────┐
          ▼                                          │
   read input  ──►  update the world  ──►  draw it  ─┘   (about 60 times a second)
   (taps, keys)     (physics, rules)       (one picture)
```

The browser gives you the loop through `requestAnimationFrame(fn)`: "call `fn` just before you next paint the
screen". You call it again at the end of `fn` to keep going.

You could do all of this with only the browser's own tools, but 3D maths and physics are a lot of work, so Baby
Toss uses two libraries:

- **[three.js](https://threejs.org/)** draws 3D scenes on the canvas.
- **[cannon.js](https://schteppe.github.io/cannon.js/)** simulates physics: gravity, bouncing, things knocking
  into each other.

These two **know nothing about each other**. That's the most important idea in this tutorial:

> For everything that moves, there are two objects: a **body** in the physics world (an invisible sphere or box
> that falls and bounces) and a **mesh** in the three.js scene (what you see). Every frame, the game copies each
> body's position and rotation onto its mesh.

---

## 3. Your first game in 100 lines

Before reading Baby Toss (about 1,000 dense lines), read [`tutorial/starter.html`](tutorial/starter.html). It's a
complete tiny game built the same way: tap to throw a ball, and score a point when one stops inside the ring.
Open it the same way as Baby Toss: http://localhost:8000/tutorial/starter.html.

It has every piece Baby Toss has, just smaller:

| Piece | In the starter | In Baby Toss |
|---|---|---|
| Drawing setup | renderer, scene, camera, one light | the same, plus a sky, a sun, shadows |
| Physics setup | a world with gravity, a ground plane | the same, plus materials, a fence, cans |
| Things that move | a ball: one mesh + one body | a baby: 7 bodies tied together (a ragdoll) |
| Rules | `checkBall`: stopped inside the ring? | `watchYard`, `canFor`, `addPoints`, turns, bust |
| Input | tap = throw | pull back like a slingshot, 7 grips, keyboard |
| Loop | step physics, copy bodies to meshes, draw | the same, plus timers, crying, fireworks |

Here's the heart of it, the part to understand first:

```js
// a ball is a mesh (what you see) and a body (what moves)
const mesh = new THREE.Mesh(new THREE.SphereGeometry(0.1), new THREE.MeshLambertMaterial({ color: 0xff3d7f }));
scene.add(mesh);
const body = new CANNON.Body({ mass: 0.2, shape: new CANNON.Sphere(0.1) });
body.position.set(0, 1.2, 0.5);
body.velocity.set(0, 3, -6.4);        // throw it: up and away
world.addBody(body);

function frame() {
  world.step(1 / 120, dt, 10);                 // move the physics forward
  mesh.position.copy(body.position);           // copy body -> mesh
  mesh.quaternion.copy(body.quaternion);       // (rotation too)
  renderer.render(scene, camera);              // paint
  requestAnimationFrame(frame);                // and again
}
```

**Try these in the starter**, one at a time, to get a feel:

- Change `world.gravity.set(0, -9.82, 0)` to `-2`. Moon throws.
- Change the throw's velocity. Which number makes it go higher? Further? Sideways?
- Make the ball a box: `THREE.BoxGeometry(0.2, 0.2, 0.2)` for the mesh and
  `new CANNON.Box(new CANNON.Vec3(0.1, 0.1, 0.1))` for the body (cannon wants *half* sizes).
- Make the mesh bigger than the body. It'll look like it sinks into the grass. That's the two worlds disagreeing.
- Add a second ring worth 3 points further back, and check both in `checkBall`.

---

## 4. How Baby Toss is laid out

```
baby-toss/
  index.html         the whole game
  README.md          how to play
  GLOSSARY.md        the game's words ("grip", "bust", "body in", "late in")
  docs/SPEC.md       what the game does: the source of truth
  docs/DECISIONS.md  why things changed, newest at the bottom
docs/CODING_STANDARDS.md   how the code is written, for every game
```

`index.html` goes from top to bottom like this:

1. **`<style>`**: all the CSS for the menus and scoreboard.
2. **The HTML**: the canvas (`#scene`), then the overlays: the setup card (`#menu`), the scoreboard (`#hud`),
   the grip buttons (`#grips`), the pause window (`#settings`), the winner screen (`#over`).
3. **The two libraries**, as `<script src=...>` tags.
4. **One big `<script>`**, wrapped in `{ }` so its variables don't leak into the page. It's split into labelled
   sections. Search for `/* ----------` to jump between them:

| Section | What it does |
|---|---|
| (top) | `MSG` (all the wording) and small helpers like `$` (find an element) and `store` (settings in `localStorage`) |
| `renderer & scene` | the three.js setup and the camera |
| `the yard` | sky, grass, fence, house, trees, and the `CANS` table |
| `physics` | the cannon.js world, materials, ground and fence |
| `cans` | builds each can, its look and its physics walls; the wobble |
| `the doll: a ragdoll` | `PART_DEF`, `JOINTS`, the baby's looks, `spawnDoll`, `canFor` |
| `grips` | the `GRIPS` table: how each way of holding the baby throws |
| `held doll and aim guide` | the baby in your hand and the dashed line on the grass |
| `fireworks` | the over-the-fence fireworks |
| `sound` | every sound, made from maths (no sound files) |
| `game state` | the rules: turns, scoring, bust, rounds, tiebreaks |
| `input` | the pull-back throw, and the keyboard |
| `loop` | `frame()`, the heartbeat |
| `graphics quality` | lowers the detail on slow devices |
| `menu & settings UI` | the setup screen, players list and switches |

Notice that **numbers you'd want to tune are tables**, not scattered through the code: `CANS`, `PART_DEF`,
`JOINTS`, `GRIPS`, `PULL`, `QUALITY`, `FOOT_SHAPE`. Changing the game usually means changing a row in a table.

---

## 5. Drawing: three.js

### The basic setup

Every three.js program has the same four things:

```js
const renderer = new THREE.WebGLRenderer({ canvas, antialias: true }); // paints onto the canvas, using the GPU
const scene = new THREE.Scene();                                       // the list of everything to draw
const camera = new THREE.PerspectiveCamera(48, aspect, 0.05, 200);     // field of view, shape, nearest, farthest
camera.position.set(0, 1.6, 1.3);                                      // where your eyes are
camera.lookAt(0.1, 0.45, -6.2);                                        // what they look at
// ...add things to the scene...
renderer.render(scene, camera);                                        // paint one picture
```

**Units and directions.** Baby Toss uses metres. **y is up**, **x is to the right**, and **-z is away from you**.
The camera stands 1.6 m tall (eye height) at the throwing spot, and the cans are 5 to 7.5 m away (negative z).
Pick real-world units for your game and stick to them: physics with real gravity (9.82 m/s²) then just works.

### Meshes: geometry + material

Anything visible is a `Mesh`, which is a **geometry** (the shape) plus a **material** (how its surface looks):

```js
const head = new THREE.Mesh(
  new THREE.SphereGeometry(1, 32, 22),                                 // radius 1; 32×22 slices (smoothness)
  new THREE.MeshStandardMaterial({ color: 0xefb895, roughness: 0.42 })
);
head.scale.set(0.072, 0.075, 0.07);                                    // squash a unit sphere into a head
head.position.set(0, 0.135, 0);
scene.add(head);
```

Baby Toss builds everything from a few simple shapes:

- **Spheres, scaled**: the baby's head, body, hands, feet and toes. The game makes **one** unit sphere and reuses
  it everywhere with different scales (the `mk(geometry, material, sx, sy, sz)` helper). Making shapes once and
  sharing them keeps the game fast.
- **Cylinders**: arms, legs, neck. The legs are stretched between the hip and the foot every frame
  (`drawLeg`), so they bend as the ragdoll flops.
- **Lathe shapes**: the cans. A `LatheGeometry` spins a 2D outline around an axis, like a pot on a potter's wheel.
- **Groups**: a `THREE.Group` holds several meshes so they move together. A baby's head is a group of the head,
  ears, eyes, eyelids, hair and mouth.

Materials, roughly from cheapest to most expensive:

- `MeshBasicMaterial`: flat colour, ignores light. Used for the aim line and the inside of the open mouth.
- `MeshLambertMaterial`: matte. Used in the starter.
- `MeshStandardMaterial`: realistic. `roughness` (0 = mirror, 1 = chalk) and `metalness`. Used for most things.
- `MeshPhysicalMaterial`: adds `clearcoat`, a glossy top layer. Used for the vinyl skin and the glassy eyes.

### Textures drawn with code

A **texture** is a picture wrapped onto a mesh. Baby Toss has no image files for its textures (only the medal is
a photo). It **draws** them with the browser's 2D canvas, then hands the canvas to three.js. That's what the `tex`
helper does:

```js
function tex(w, h, draw) {
  const c = document.createElement('canvas');
  c.width = w; c.height = h;
  draw(c.getContext('2d'), w, h);            // draw with the normal 2D canvas API
  return new THREE.CanvasTexture(c);
}
const stripes = tex(128, 128, (g, w, h) => {
  g.fillStyle = '#f4f4f4'; g.fillRect(0, 0, w, h);
  g.fillStyle = 'rgba(0,0,0,.18)';
  for (let y = 14; y < h; y += 32) g.fillRect(0, y, w, 18);
});
```

The onesie knits, the fence boards, the sky, the scuffs on the cans and the "BABY TOSS" lettering on the tall can
are all drawn this way. The lettering even uses the logo's SVG path data (`Path2D`) straight from the HTML.

### Light and shadow

- A **hemisphere light** gives soft light from the sky above and the grass below.
- A **directional light** (the sun) casts shadows. Shadows are expensive, so only the sun casts them, and only
  objects with `castShadow = true` make them.
- The **sky picture** is also used as the scene's environment map, so shiny things like the metal can reflect the
  sky.
- Colours are picked by eye, but three.js lights in "linear" colour, so the game converts them (`lin()`).
  Without that, everything looks washed out.

---

## 6. Physics: cannon.js

### The world and bodies

```js
const world = new CANNON.World();
world.gravity.set(0, -9.82, 0);
```

A **body** has a **mass** and one or more **shapes**:

- `mass: 0` means the body never moves: the ground, the fence, the cans.
- Shapes are simple on purpose: spheres, boxes and planes. Physics engines are fast and stable with simple shapes.
  **The physics shapes don't have to match what you draw.** The baby's head is drawn as a detailed face but
  simulated as one sphere. Each can is drawn as a smooth round can but simulated as 20 thin boxes in a ring plus
  a floor box. Nobody can tell.

### Materials: bounce and grip

How two things react when they touch is set **per pair** of materials:

```js
// friction 0.3 (slippery), restitution 0.5 (bouncy): hard vinyl on metal clanks and bounces
cm(M.vinyl, M.metal, 0.3, 0.5);
// friction 0.95, restitution 0.04: the stuffed body on grass thuds and stays put
cm(M.stuffed, M.grass, 0.95, 0.04);
```

This is why a baby that lands body-first stays in a can, and one that lands head-first might bounce out. The real
dolls have a stuffed body and a hard vinyl head, arms and feet, so the game gives them different materials.

### Stepping the world

```js
world.step(1 / 240, dt, 14);
```

Read it as: "move time forward by `dt` (the real time since the last frame), in steps of exactly 1/240 of a
second, doing at most 14 steps". **Fixed steps** matter. With a varying step, a throw would go further on a fast
computer than a slow one. Small steps (240 a second) stop fast-moving babies from passing through thin can walls.

### Listening for hits

Bodies fire a `collide` event. Baby Toss uses it for impact sounds, the can's wobble, and making the baby cry
when it lands hard:

```js
body.addEventListener('collide', e => {
  const speed = Math.abs(e.contact.getImpactVelocityAlongNormal());
  if (speed > 1) sfx.impact(e.body.material, speed);   // what it hit and how hard
});
```

### Physics that isn't real

The cans "wobble but never tip over". Doing that for real is fiddly and could change whether a baby is in. So the
physics can never moves, and only its **picture** rocks (`wobble` and `rockCans`). When it looks right, a fake is
fine.

---

## 7. The ragdoll baby

A **ragdoll** is a character made of several bodies held together by joints, so it flops like a doll.

The baby has 7 bodies, all spheres (see `PART_DEF`):

```
          0 head (vinyl)
              │  neck
   3 arm ─ 1 chest ─ 4 arm        (arms are vinyl, swing at the shoulders)
              │  waist
           2 hips (stuffed)
            /   \   floppy stuffed legs
       5 foot   6 foot  (vinyl)
```

Each line in that picture is a row in `JOINTS`, and is two things:

1. **A pin** (`CANNON.PointToPointConstraint`): it keeps a point on one body stuck to a point on the other, like a
   ball-and-socket joint. Pins alone give a doll that folds up like wet laundry.
2. **A spring** (`applySpring`, run before every physics step): it gently pulls each part back towards its resting
   pose. `k` is how stiff it is and `c` is how quickly it stops wobbling. The neck and waist are stiff (`k` 40–50).
   The arms and legs are loose (`k` 4–6), so they flail.

```js
// joint: child a hangs on parent b at pivot; a spring pulls the child's far point back to its resting place
{a:0, b:1, piv:[0,.09,0],  far:[0,.07,0], k:40, c:1.2},   // head on chest: stiff
{a:5, b:2, piv:HIP[0],     far:[0,0,0],   k:6,  c:.2},    // left foot on hips: floppy
```

**Living babies** (the setting) is just extra random forces on the arms and legs, added in the same place as the
springs. The looks (onesie, face, toes) are drawn on top and copied from the 7 bodies every frame by `drawBodies`.

**Throwing** (`doThrow`) creates a new ragdoll where the held baby was, gives every part the same velocity, and
adds spin (`omega`) depending on the grip. Holding by the feet flips it end over end. Holding by a hand
cartwheels it.

---

## 8. The loop

Here's `frame()`, slightly simplified. Everything happens from here:

```js
function frame() {
  const dt = Math.min(clock.getDelta(), 0.05);  // time since last frame, capped: if the tab was hidden, don't
                                                // jump 30 seconds ahead in one go
  if (!S.paused) {
    world.step(1 / 240, dt, 14);                // 1. physics
    for (const d of dolls) { lifeTick(d, dt); drawBodies(d); face(d.v, d, t); }  // 2. copy bodies to meshes,
                                                //    blink, cry
    rockCans(dt); fireworksTick(dt);            // 3. things that only look like they move
    watchYard(dt);                              // 4. rules: has a baby stopped? is it in a can?
    // 5. timers: the "Player 2's turn" banner, the wait between throws, the end of the turn...
  }
  renderer.render(scene, camera);               // 6. paint
  requestAnimationFrame(frame);
}
```

**Never use `setInterval` for a game loop.** `requestAnimationFrame` matches the screen's refresh and pauses in a
hidden tab, saving battery. And always multiply by `dt` ("per second" × seconds since the last frame), so the game
runs at the same speed at 30 or 120 frames a second.

**Timers live in the loop**, not in `setTimeout`. For example `S.wait = 1.5` then `S.wait -= dt` each frame. That
way pausing the game pauses its timers too.

---

## 9. Input: throwing with a finger or a mouse

Use **pointer events** (`pointerdown`, `pointermove`, `pointerup`). One set of code then handles mouse, touch and
pen. (The old `mousedown` and `touchstart` events mean writing everything twice.)

The throw works like a slingshot:

```js
canvas.addEventListener('pointerdown', e => { drag = { x: e.clientX, y: e.clientY }; });
addEventListener('pointermove', e => {
  if (!drag) return;
  aim.power = clamp((e.clientY - drag.y) / full, 0, 1);   // pull down = more power
  aim.yaw   = -(e.clientX - drag.x) / width * gain;       // pull left = aim right
});
addEventListener('pointerup', () => { doThrow(); drag = null; });
```

Lessons learned while tuning it:

- **Pull down, not swipe up.** Swiping up puts your finger over the cans you're aiming at.
- **Ignore the last moment.** Fingers wobble as they lift off, so the throw uses where the finger was 80 ms before
  it lifted (`PULL.lookback`).
- **Show the aim, not the landing.** A dashed line shows the direction and a meter shows the power. Showing where
  it would land would make it too easy.
- **`touch-action: none`** on the canvas (in the CSS) stops the phone from scrolling or zooming when you drag.
- **Keyboard too.** Number keys pick a grip, the arrow keys aim, and Space throws. A game that only works by
  dragging locks some people out.

---

## 10. Rules and game state

### Keep the rules apart from the drawing

All the game's state is one plain object, `S`:

```js
const S = { phase: 'menu', players: [], turn: 0, round: 0, left: 3, turnStart: 0, ... };
```

The rules are plain functions that read and change `S` and never touch three.js or cannon.js:

```js
function addPoints(pts) {                         // returns 'bust', 'target' or null
  const p = current();
  if (p.score + pts > setup.target) { p.score = S.turnStart; return 'bust'; }
  p.score += pts;
  return p.score === setup.target ? 'target' : null;
}
```

This keeps the rules easy to read, easy to check against the spec, and easy to test without a browser later.

### Phases (a state machine)

The game is always in exactly one **phase**, and the loop and input check it:

```
menu → banner ("Player 1's turn") → aim → flying → between → aim → ... → settling → banner (next player) → ... → over
```

The input code ignores taps unless `S.phase === 'aim'`, so you can't throw while a baby is in the air. When you add
a feature, ask "which phase can this happen in?"

### Turning physics into points

Physics never says "the baby is in the can". The game has to look and decide. `watchYard` runs every frame:

1. **Has the baby stopped?** Its head and body have moved slower than 0.15 m/s for half a second (`STOPPED`).
   Waiting for "exactly zero" never happens, because physics always jiggles a little.
2. **Is it in a can?** `canFor` checks that both body spheres (chest and hips) are inside the can's walls and
   below its rim. The head, arms and legs can stick out.
3. **Score it**, or remember it might still be knocked in later (a "late in").

A real bug from building this: the first rule needed *every* part inside. A baby whose foot slid in a second after
its body had stopped scored nothing, because the game had already judged it. The fix was to judge the same parts
the "stopped" check watches. **When rules depend on physics, think about timing.**

---

## 11. Sound without sound files

All the sounds are made from maths with the browser's **Web Audio API**: the can's bong, the thud on grass, the
fireworks, the score chimes and the babies' cries.

```js
const ac = new AudioContext();                // must be created or resumed after a tap (browser rule)
const osc = ac.createOscillator();            // a pure tone
const gain = ac.createGain();                 // a volume knob
osc.frequency.value = 523;                    // C5
gain.gain.setValueAtTime(0.2, ac.currentTime);
gain.gain.exponentialRampToValueAtTime(0.001, ac.currentTime + 0.4);   // fade out over 0.4 s
osc.connect(gain).connect(ac.destination);
osc.start(); osc.stop(ac.currentTime + 0.4);
```

That's how the score chime works. Bigger sounds are built **sample by sample** into a buffer:

- **The metal can** (`canBong`) is about 20 sine waves at the uneven pitches a real metal cylinder rings at
  (`CAN_MODES`). The high ones die away faster. A short burst of noise at the start is the knock.
- **The cries** are a small model of a voice: buzzing pulses at a baby's pitch, shaped by the resonances of a
  small throat (`VOWEL`). The pulses wobble a little in timing, because a perfectly regular tone sounds fake.

**Browsers only allow sound after the player taps or presses a key.** That's why `audio()` is called from the
first `pointerdown`.

---

## 12. Screens, wording and accessibility

- **Screens are HTML, not canvas.** The setup card, scoreboard, pause window and winner screen are normal HTML
  with CSS, shown and hidden with the `hidden` attribute. Buttons are real `<button>`s, so they work with a
  keyboard and screen readers for free.
- **All wording is in `MSG`** at the top of the script. Changing what the game says means editing one table. It
  also keeps the user's own words (the tagline, the rules) in one place.
- **The 3D yard is only a picture** to a screen reader, so anything important is also written in HTML with
  `aria-live`, which reads changes out: whose turn it is, the score, the grip.
- **Reduce motion**: when the device asks for less motion (`prefers-reduced-motion`), page animations like the
  medal popping in are turned off. The physics stays.
- **Tap targets are at least 44 px**, and everything works from 320 px wide.

---

## 13. Making it run on phones

3D in a browser works on any modern phone, but phones are slower than computers and their screens have many more
pixels. Baby Toss does four things:

1. **Caps the pixel ratio at 2.** A phone may have 3 screen pixels per CSS pixel. Drawing 9 times the pixels for
   no visible gain is a waste: `renderer.setPixelRatio(Math.min(devicePixelRatio, 2))`.
2. **One light casts shadows.**
3. **Clears the yard between turns**, so there are only ever a few ragdolls to simulate.
4. **Lowers its own quality** (`graphics quality` section). It measures the average frame time over 2 seconds. If
   that's slower than 1/40 of a second, it drops one step in `QUALITY`: lower resolution and softer shadows, then
   no grass blades. It never steps back up, so the picture doesn't keep changing.

Also: `viewport-fit=cover` and `env(safe-area-inset-*)` in the CSS keep buttons clear of the iPhone's notch and
home bar, and `resize()` keeps the camera's shape matched to the screen. On a narrow phone it also widens the
field of view so the cans still fit.

---

## 14. How it was built: the process

Baby Toss was built by Claude (an AI) and its inventor working together, in short rounds. The process matters as
much as the code:

1. **Spec first.** Before any code, `baby-toss/docs/SPEC.md` described the game: the cans and their points, the
   rules, the grips, the screens. Each rule is marked **(user)** if the inventor decided it, or **(Claude's call)**
   if it was a guess to check. Open questions are listed at the end. When something new comes up, the spec changes
   first, then the code, in the same commit.
2. **Write down why** in `docs/DECISIONS.md`, newest at the bottom: "the feet looked like balloons, so…". Six
   months later you'll want to know why a number is what it is.
3. **A glossary** (`GLOSSARY.md`) fixes the game's words: a **grip**, a **bust**, **body in**, a **late in**. The
   same words go in the code (`grip`, `bust`), so the spec, the code and the conversation all match.
4. **Small steps, each played.** The yard and one can first, then a baby that falls, then throwing, then scoring,
   then turns and rules, then looks and sound. After each step it was loaded in a real browser, at computer and
   phone sizes, and played.
5. **Checking without a person.** Claude can't see a screen, so it loads the game in **headless Chromium** (a
   browser without a window) through [Playwright](https://playwright.dev/), takes screenshots and looks at them.
   For rules, it reaches into the running game and places babies exactly (a foot hanging out, a body above the
   rim) to check that the score comes out right. You can do the same from the browser console.
6. **Then the inventor plays it** and reports what feels wrong ("it took a second for the foot to slide in and I
   got 0 points", "the feet look like balloons"). Each report becomes a spec change, a fix, and a line in
   DECISIONS.
7. **One branch per change**, merged to `main` when it's right. Every merge to `main` updates the website.

---

## 15. Recipes: changing Baby Toss

All in `baby-toss/index.html`. Search for the name in bold. Remember to update `docs/SPEC.md` to match.

**Move a can or change its points.** Edit **`CANS`**. `x` is left/right, `z` is the distance (negative = away),
`r` is the radius, `h` the height, all in metres:

```js
{id:'front', pts:2, x:-.45, z:-5.0, r:.25, h:.6, mat:'plastic', look:'black'},
```

Change the rules text in the HTML (`class="rules"`) and the scoreboard text (`canValues` in **`MSG`**) to match.

**Make throws stronger or weaker.** **`SPEED_MIN`** and **`SPEED_MAX`** are the launch speeds in m/s at no pull
and at full pull.

**Change how a grip throws.** Each row of **`GRIPS`**:

- `pitch`: launch angle in radians (0.8 is about 45°)
- `speed`: multiplies the launch speed
- `scatter`: how inaccurate it is
- `spin`: rotation in radians per second (`x` = end over end, `z` = cartwheel)
- `curve`: a sideways pull while it flies
- `note`: the hint shown under the buttons

**Add a grip.** Copy a row in **`GRIPS`** with a new `id` and `label`. `pt` is the point on the doll you're
holding, in the doll's own coordinates: `[0, .2, 0]` is the top of the head and `[0, -.2, .015]` is the feet. The
buttons are built from the table. The keyboard shortcuts `1`–`7` would need extending for an eighth.

**Make babies bouncier or deader.** The `cm(...)` lines in the **physics** section: the last number is bounce
(0 to 1), the one before it is friction.

**Floppier or stiffer babies.** `k` (stiffness) and `c` (damping) in **`JOINTS`**.

**Change wording.** Edit **`MSG`**.

**Change colours.** The menus use CSS variables at the top of `<style>` (`--pink`, `--ink`, …). The babies'
onesies are **`ONESIES`**. The yard's colours are in **the yard** section.

**Explore while it runs.** The game's variables are hidden inside `{ }`. To poke at them, temporarily add
`window.G = { S, dolls, CANS, world, camera };` just before the closing `}` of the script, reload, then try in the
console: `G.S.players`, `G.camera.position.y = 3` (bird's-eye view), `G.world.gravity.y = -2`. Take the line out
again afterwards.

---

## 16. Making your own game

A plan that works:

1. **Write the spec first**, even half a page. What does the player see? What do they do? How do you score? How
   does it end? Copy the headings of `baby-toss/docs/SPEC.md`. Writing it down shows you the questions you hadn't
   thought of ("what if two land at the same moment?").
2. **Make the folder**: `my-game/index.html`, `my-game/README.md`, `my-game/docs/SPEC.md`.
3. **Start from `tutorial/starter.html`**, not from Baby Toss. Copy it into your folder. It already has drawing,
   physics, input, a rule and a loop.
4. **Build the world with grey boxes first.** Ground, targets, walls: plain boxes and cylinders, in metres, at
   real sizes. Get the camera where your eyes would be. Don't make anything pretty yet.
5. **Get the main action feeling right.** Throwing, rolling, hitting. Tune the numbers (speed, gravity, bounce)
   until it feels like the real thing. Put those numbers in a table at the top. This is most of what makes a game
   fun, and it takes the most tries.
6. **Add the rules** as plain functions on a state object. Then add phases (menu → play → over) so input only
   works when it should.
7. **Add the HTML screens**: a start screen, a scoreboard, a game-over screen.
8. **Then make it look and sound good**: textures drawn with `tex`, better shapes, sounds with Web Audio.
9. **Try it on a real phone** early and often.

**Not every game needs 3D or physics.** A card game, a word game or a puzzle can be plain HTML and CSS with a bit
of JavaScript. A flat arcade game can draw on a canvas with the 2D API (`ctx.fillRect`, `ctx.drawImage`) and
simple maths for movement. The loop (`requestAnimationFrame` and `dt`) and the "rules apart from drawing" idea
are the same.

**Pitfalls you'll hit:**

- *Things fall through the floor.* The physics step is too big for the speed, or the floor is too thin. Use small
  fixed steps (1/120 to 1/240) and thick boxes for walls.
- *Something sinks or floats.* The mesh and the body don't match in size or position.
- *Black or invisible objects.* There's no light, the object is behind the camera, or it's too small or too big.
  Check the units.
- *Everything is huge or tiny.* Mixing centimetres and metres. Use metres everywhere.
- *No sound.* The `AudioContext` wasn't started from a tap.
- *Fast on your computer, slow on your phone.* Cap the pixel ratio, cut shadows, use fewer and simpler meshes.

---

## 17. Putting it on the website

The website is https://worst-games.netlify.app. It's the root `index.html` (one card per game), published by
Netlify from the `main` branch. To add a game:

1. Add a card to the root `index.html`: copy the Baby Toss `<li class="card">` and change the name, tagline and
   link.
2. Add your game to the copy command in `netlify.toml`. It copies only the playable pages into `dist`, so the
   docs aren't published:
   ```toml
   command = "rm -rf dist && mkdir -p dist/baby-toss dist/my-game && cp index.html dist/ && cp baby-toss/index.html dist/baby-toss/ && cp my-game/index.html dist/my-game/"
   ```
3. Work on a branch, then merge to `main`. Netlify publishes it a minute later. The free plan allows about 20
   deploys a month, so batch your changes.

---

## 18. Where to learn more

- **three.js manual**: https://threejs.org/manual/ (start with "Fundamentals", "Primitives", "Materials",
  "Lights", "Shadows"). Baby Toss uses an older version, r128, loaded as a plain `<script>`. New three.js is
  loaded as a JavaScript module (`import * as THREE from 'three'`) and a few names have changed. The ideas are the
  same.
- **cannon.js docs**: https://schteppe.github.io/cannon.js/docs/. cannon.js is no longer updated. A maintained
  copy, **cannon-es**, works almost the same way. Larger engines worth knowing: Rapier and Ammo.js.
- **MDN** (the browser's own reference), for:
  - `requestAnimationFrame`: https://developer.mozilla.org/docs/Web/API/Window/requestAnimationFrame
  - Pointer events: https://developer.mozilla.org/docs/Web/API/Pointer_events
  - The 2D canvas: https://developer.mozilla.org/docs/Web/API/Canvas_API/Tutorial
  - Web Audio: https://developer.mozilla.org/docs/Web/API/Web_Audio_API
  - Game development: https://developer.mozilla.org/docs/Games
- **This repo's own docs**: `docs/CODING_STANDARDS.md` (how the code is written) and `baby-toss/docs/SPEC.md`
  (a full game spec to copy the shape of).
