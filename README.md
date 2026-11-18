# Embervale — Isle of Star Shards (playable demo)

A cozy, cinematic Zelda-like adventure prototype: explore a floating-island world, fight five legendary bosses, collect loot, and time-travel to a modern city, the Jurassic and Ancient Egypt.

This repository holds the **web prototype**. It is a single self-contained file built with JavaScript and [Three.js r128](https://threejs.org/) (WebGL). It exists to test the feel of the game before a production rebuild in **Godot** or **Unity (C#)**. See [`docs/DESIGN.md`](docs/DESIGN.md) for the design notes and the porting plan.

## Run it

Open `index.html` in a modern browser (Chrome, Safari, Edge, Firefox). It also runs on iPhone and Android with on-screen touch controls.

The page loads Three.js from a CDN (cdnjs, with jsDelivr and unpkg as fallbacks), so it needs an internet connection the first time.

For local development, serve the folder instead of opening the file directly:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Controls

| Action | Keyboard / mouse | Touch |
| --- | --- | --- |
| Run | W A S D | Left joystick |
| Sprint | Shift | — |
| Jump | Space | ⤒ |
| Sword | Click or J | ⚔ |
| Talk / open | E | E |
| Look around | Drag mouse, arrow keys | Drag right side of screen |
| Zoom | Scroll or Z | 🔍 |
| Time-travel | T | 🌀 |
| Cinematic effects on/off | C | 🎬 |

## What's in the demo

- **Embervale island:** village, NPCs with dialogue, the Star Shard quest, the Sky Gate ending, treasure chests, breakable pots, gear (Star Compass, Swift Boots, Ember Blade, Guardian Shield, Heart Container).
- **Five legendary bosses:** Thornback Golem, Emberwing, Moonveil Wraith, Tidecaller and Thornbloom Matriarch, each with its own attacks, lair and trophy.
- **Three time-travel realms:** Neo Ember City (traffic, monorail, blimp), the Jurassic Wilds (T-Rex, Brachiosaurus, Triceratops, Stegosaurus, volcano) and the Sands of Kemet (pyramids, Sphinx, temple, Nile, village and market).
- **Visuals:** toon shading with painterly trees, a shader sky and water, and cinematic post-processing (bloom, colour grading, lens flare, vignette, film grain, letterbox bars, light shafts).
- **Performance:** instanced trees in culled chunks, pooled particles, adaptive resolution, and automatic fallback when the frame rate drops.

## Project status

Prototype only. All models are built from simple shapes in code; the production version will use proper 3D models (e.g. made in Blender).

## Repository layout

```
index.html        the whole playable prototype (HTML + CSS + JavaScript)
docs/DESIGN.md    game design notes and the Godot / Unity porting plan
```
