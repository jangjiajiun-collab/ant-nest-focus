# Ant Nest Focus

A single-file, pixel-art 3D focus timer. Your task becomes a glowing seed; a 25-minute focus session sends the colony to carry it home. Leave early and the seed dims and the ants walk home empty-handed. Nothing is ever lost.

- **File:** `index.html` (no build step, no external models or textures; all geometry is procedural)
- **Run:** open `index.html` in a browser, or serve the folder with any static server
- **Stack:** three.js r160 (import map from jsDelivr), OrbitControls, Google Fonts (Silkscreen, Rubik, JetBrains Mono)

## Rendering pipeline
- Orthographic isometric camera, 31 degree elevation, azimuth snaps in quarter turns
- Scene renders to a small target (~480x300, no anti-aliasing, nearest filter), upscaled with `texelFetch`
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
