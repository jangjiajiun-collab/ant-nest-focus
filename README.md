# Ant Nest Focus

A single-file, pixel-art 3D focus timer. Your task becomes a glowing seed; a 25-minute focus session sends the colony to carry it home. Leave early and the seed dims and the ants walk home empty-handed. Nothing is ever lost.

- **File:** `index.html` (no build step, no external models or textures; all geometry is procedural)
- **Run:** open `index.html` in a browser, or serve the folder with any static server
- **Stack:** three.js r160 (import map from jsDelivr), OrbitControls, Google Fonts (Silkscreen, Rubik, JetBrains Mono)

## Rendering pipeline
- Orthographic isometric camera starts at 31 degree elevation; orbit, pan and zoom are free (double click resets)
- Scene renders to a small target (about 420 px on the short side, no anti-aliasing, nearest filter), upscaled with `texelFetch`
- Post pass: depth outlines (neighbour more than 0.5 units behind darkens 0.5x), 4x4 Bayer dither, fixed 64-colour palette matched in OKLab
- Hard sun shadows; day/dusk/night follows the real clock

## Simulation
- Soil is an instanced cross-section grid; tunnels and chambers are carved cells. Digging removes cells over time.
- Navigation graph of nodes and edges; ants follow plans (go / wait / move / fn). Pheromone per edge decays and biases wandering.
- Ants: 3 body boxes plus 6 legs with two-bone IK and tripod stepping, drawn as one InstancedMesh.
- Focus loop: task -> seed -> foragers walk out -> 25 min -> carry to deepest chamber -> food +1. Pause or leaving the tab dims the seed; below 20% the trip is abandoned (no penalty).
- Economy: food hatches workers (egg, larva, pupa, worker), digs chambers (deeper costs more food and needs more workers), upgrades the glass case.
- Reference: Tschinkel 2004, J. Insect Science 4:21, and Tschinkel 1999, Ecological Entomology 24:222 (nest size scales with colony size).

## Status
The logic was exercised headlessly against a stubbed three.js. The WebGL rendering has not been visually verified yet, so expect tuning of light levels, outlines and ant scale.

## Debug hooks (browser console)
`ANT.setHour(h)`, `ANT.fast(n)`, `ANT.rain()`, `ANT.wind()`, `ANT.food(n)`, `ANT.drop(text)`, `ANT.start()`

## Changelog
- **Taste pass 1 (motion):** rounded dark corner fillets where soil meets a carved hole; per-ant speed variance, head wobble, belly drag, 0.5s antenna touch at the seed, 1s look-left/right at the nest entrance, ~70% of idle time spent in slight idle; seed pulses 1.0 to 1.15 over 2s with two orbiting light motes.
- **Taste pass 2 (typography):** two-line 48px title, "FEED" kicker, vertical right-edge scoreboard (FOOD / WORKERS / TODAY), task entry is a hanging wooden sign (tap to edit), controls bottom-left, collapsed 9px footer bottom-right. Render pipeline untouched.
- **Pass 3:** finer render target (420 px short side) and half-size soil cells; free camera (no polar lock, no azimuth snap, zoom 0.4x-14x, wider pan); ants rebuilt with a slim thorax, petiole, egg-shaped gaster, long two-bone legs, mandibles and elbowed twitching antennae, each ant a slightly different dark brown; roots in the topsoil and pebbles/husks on chamber floors.
- **Pass 4 (mood and scale):** dark room with one warm hard-shadow lamp over the tank and a vignette in the post pass (dark edges, bright middle); sky, clouds and stars removed; ants scaled to 0.8 and slowed; tunnels narrowed to about two ant-widths and chambers flattened so proportions read as a real ant farm; default view is closer on the tank.
- **Pass 5 (tank, not island):** the floating island is gone. The colony lives in an indoor rainforest vivarium: four glass walls, glass lid, black frame and base tray, pure black surroundings. A moss surface with ferns, broadleaf plants, driftwood, a fallen log and hanging vines; one warm lamp with soft light shafts pours down into the tank. Rain, wind, trees, butterflies, fireflies and the leaf cover are switched off.
- **Pass 6 (a room around the tank):** the tank now sits on a wooden desk in the corner of a dim room: night-city window, posters, string lights, a stack of books and an open book with a pencil, pink over-ear headphones, a laptop with a softly lit screen, a steaming mug, a potted plant and a pencil cup. The warm lamp still lights the tank first; the room only gets spill and small glows.
- **Pass 7:** clearer glass (opacity 0.1 to 0.035) and a bit more light inside the tank; the light/dark and season buttons are gone (the room is always night-lit and the tank has one fixed warm, humid look).
