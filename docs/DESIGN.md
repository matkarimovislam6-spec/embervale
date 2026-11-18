# Embervale — design notes and porting plan

This document captures how the web prototype works, so it can be rebuilt in **Godot 4 (GDScript)** or **Unity (C#)** without reverse-engineering the JavaScript. Line numbers drift, so search `index.html` for the section headers quoted below (they appear as `/* ===== ... ===== */` comments).

## 1. Core loop

1. Arrive in Embervale, talk to **Elder Moss** to start the quest.
2. Explore, open chests, smash pots, collect glimmers (currency) and gear.
3. Find the three **Star Shards** (Thornback Hill, the sandy shore, the ruins chest), shown by golden light beams.
4. Optionally defeat the five **Legendaries** for trophies that improve the hero.
5. Time-travel (🌀) to the three realms to explore and find one relic in each.
6. Bring the shards to the **Sky Gate** to finish the demo.

## 2. Hero

| Stat | Value |
| --- | --- |
| Hearts | 5 (10 half-hearts); +1 per Heart Container / Golem trophy |
| Run speed | 8 units/s, ×1.5 sprint, ×1.3 with Swift Boots |
| Jump | vertical speed 9, gravity 24 |
| Sword | 0.32 s swing, hit window 0.06–0.24 s, reach ~2.9 units in front |
| Damage | 1 (+1 Ember Blade, +1 Emberwing trophy) |
| Invincibility after hit | 1.1 s (1.8 s with Moonveil Lantern) |
| Block chance | 30% Guardian Shield, +20% Tidecaller trophy |

Death respawns the hero in the village with full hearts; progress is kept.

## 3. World

- **Terrain:** a height function `H(x, z)` (sum of sines, island fall-off, Gaussian hill, flattened plateaus for the village, ruins and Sky Gate). Port it as a heightmap texture or a procedural mesh, and reuse the same function for gameplay ground height.
- **Points of interest:** village (0, 12), Thornback Hill (42, -38), Sky Gate (-58, 40), ruins (48, 42).
- **Realms:** each realm sits far away on the X axis (city at x = 2000, Jurassic at 4000, Egypt at 6000) and has its own ground function, bounds, sky colours and fog. In an engine, make each realm a **separate scene** loaded on time-travel.

## 4. Loot

| Item | Rarity | Effect |
| --- | --- | --- |
| Star Compass | Legendary (always in the first chest) | Reveals all treasure chests with blue beams |
| Ember Blade | Epic | Sword deals +1 damage |
| Guardian Shield | Epic | 30% block chance |
| Swift Boots | Rare | +30% run speed |
| Heart Container | Rare | +1 max heart, full heal |
| Glimmer Hoard | Common | +10–30 glimmers, heal 1 heart |

Glimmers are worth 1 (green), 5 (blue) or 20 (pink). Pots drop glimmers (55%) or hearts (30% when hurt).

## 5. Legendary bosses

All bosses wake when the hero enters their lair (14–22 units), show a name card and health bar, and reset to full health if the hero leaves (50 units from the lair). Each defeat drops a trophy, 12 glimmers and 2 hearts.

| Boss | HP | Look | Attacks | Weak point / counter | Trophy |
| --- | --- | --- | --- | --- | --- |
| Thornback Golem | 18 | Lilac stone giant, moss back, teal crystals, amber core | Punch (2 dmg); ground slam shockwave ring (2 dmg) | Jump over the shockwave | +1 max heart |
| Emberwing | 14 | Crimson/gold wyvern, orange bat wings | Fireballs (2 dmg, can be slashed); dive-bomb (2 dmg) | Only hittable after it lands for ~2.8 s | +1 sword damage |
| Moonveil Wraith | 12 | Translucent violet robe, bone mask, teal eyes, halo, orbiting wisps | 3 homing wisps (1 dmg, can be slashed); claw swipe (2 dmg); fade + teleport | Invulnerable while fading | Longer invincibility |
| Tidecaller | 16 | Coral crab, turquoise spots, one huge claw, eye stalks | Double claw snap (2 dmg); wind-up charge (2 dmg) then dazed | Shells up (blocks) after 3 quick hits | +20% block |
| Thornbloom Matriarch | 16 | Giant magenta flower, toothed mouth, thorny vines | Arcing seed volleys (1 dmg); vine slam on red warning circle (2 dmg) | Rooted; enrages below 50% HP | Health regeneration |

Boss behaviour is a small state machine per boss (`behave()` in the source). In Godot, use a `StateMachine` node or an `AnimationTree`; in Unity, an Animator plus a simple state class.

## 6. Time-travel realms

| Realm | Era | Highlights | Relic |
| --- | --- | --- | --- |
| Neo Ember City | Present day | Skyscrapers with lit windows, shops and awnings, traffic and pedestrians, traffic lights, monorail, blimp | Neon Microchip |
| The Jurassic Wilds | 150 million years ago | T-Rex (chases, bites 2 dmg, dazed by 4 hits), Brachiosaurus, Triceratops, Stegosaurus, Pteranodons, lake, volcano | Amber Fossil |
| The Sands of Kemet | Ancient Egypt, 2560 BC | Three pyramids, Sphinx, obelisks, temple with colossi, the Nile with boats, village, market, workers hauling stone | Golden Scarab |

## 7. Visual style

- **Shading:** 5-step toon ramp, soft semi-transparent inverted-hull outlines.
- **Palette:** golden hour; sun `#ffe2b8`, sky top `#3f9be0` to horizon `#f4dfcf`, hemisphere ground bounce `#7f68a0`.
- **Water:** shader using terrain depth for shallow/deep colour, shoreline foam, caustics, sun sparkles and fresnel.
- **Post-processing:** bloom (2 blur levels), split-tone grade (warm highlights, teal shadows), contrast 1.08, saturation 1.1, vignette, film grain, chromatic aberration at edges, sun lens flare, letterbox bars during intros.
- **Atmosphere:** drifting pollen, butterflies, birds, light shafts, floating sky islands with waterfalls.

Engine equivalents:
- **Godot 4:** WorldEnvironment (glow, tonemap, adjustments, fog), a custom toon shader or a toon ramp, inverted-hull outline as a `next_pass` material, GPUParticles3D, MultiMeshInstance3D for trees and grass.
- **Unity (URP):** a Volume with Bloom, Color Adjustments, Split Toning, Vignette, Film Grain, Chromatic Aberration, Lens Flare (SRP), plus a toon shader (e.g. via Shader Graph) and GPU instancing.

## 8. Porting plan

1. **Hero controller:** third-person movement, camera orbit, jump, sword swing with hit window.
2. **Embervale terrain:** port `H(x, z)` and the points of interest; add water and sky.
3. **One boss end-to-end** (Thornback Golem is the simplest), including the health bar and trophy.
4. **Loot and HUD:** chests, pots, glimmers, gear, hearts.
5. **Remaining bosses**, then the quest and the Sky Gate ending.
6. **Realms** as separate scenes, starting with the Jurassic.
7. **Art pass:** replace code-built shapes with Blender models; add the post-processing stack.
8. **Platform builds:** Steam (PC / Steam Deck) first, then iOS.
