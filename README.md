# Loadout Survivors

A browser survivor-like game with a deckbuilding twist. Open `index.html` in any browser. No build step. The stages have no dependencies; the 3D Dungeon mode loads [Three.js](https://threejs.org) from a CDN the first time you enter it.

## Core idea

- Before each stage, build a **loadout** of abilities and traits.
- Abilities and positive traits **cost** points. Negative traits **give points back**.
- The total must fit the stage's **budget**. Unused points become bonus coins.
- Some pairs form **combos** that change costs or effects.
- Stages can **force** or **ban** traits.
- In a run, each level-up is a small upgrade: weapon and abilities go to Lv 10, positive traits to Lv 8, and a negative trait can be **overcome** in 10 steps. Cards show the exact before → after numbers.
- Runs have elites, swarms, treasure chests, drops (heal, magnet, bomb) and a kill chain that boosts XP.
- New items come from first clears and the **gacha**.
- Loadouts can be saved to 6 slots.

## Dungeon mode

A 3D mode seen from behind and above, in the style of Minecraft Dungeons. It uses the same characters, loadouts, weapons and level-ups as the stages. It follows the dungeon design notes on floor structure, the death penalty, the first theme and enemy families:

- 100 floors in 20 themes of 5 floors are planned. Two themes are built: **Mossy Ruins** (floors 1–5) and **Flooded Aqueducts** (floors 6–10).
- Every floor is a large hand-made map (about 7–10 minutes for a full clear) with its own floor boss. The last floor of a theme ends with the floor boss and then the theme boss, back to back.
- Each floor starts you back at level 1 with your bare loadout, and each level needs 1.1× the XP of the one before (`DG_XP_GROWTH`; the stages use 1.08×). XP gems stay where enemies fall.
- Every boss has several attacks of its own (for example the Broodmother's web shots, egg sacs, web field, pounce, venom spray and spiderlings); the attack list is `patterns` in its `SKINS` entry, and the attacks live in `bossStep()`.
- Enemies get tougher the farther they are from the floor's start: from ×0.8 health and damage next to the start to ×1.35 at the far end (`DG_DIST`). Far-away enemies are also a little bigger and give more XP.
- Floor 5 is a long sealed corridor: iron gates drop in front of you and behind you, and four waves attack in each section. There is no way back.
- Mossy Ruins gimmicks: spider webs slow you (floor 2), cracked floor falls away (floor 3), moss pools poison you (floor 4). Enemy families: Ruin Revenants (melee), Stone-eater Spiders (poison), Ruin Archers (ranged).
- Flooded Aqueducts has six enemy types, and each floor mixes them differently (`packs` and `foes` in each `DG_FLOORS` entry):
  - Drowned Sailors grab you until you roll free. Harpooners aim, then throw a harpoon that drags you in.
  - Ironshell Crabs block most damage from the front, so hit them from the side or behind.
  - Bubble Sirens fire slowing bubbles and trapping bubbles. Electric Eels are fast in water and shock the water around them. Glow Jellyfish sting and burst into a shock when they die.
- Flooded Aqueducts floors:
  - Floor 6, Sluice Gate: sailors and harpooners. Currents push you along the canals.
  - Floor 7, Crab Cisterns: crabs, jellyfish and sailors. Deep water slows you and slick algae makes you slide.
  - Floor 8, Lightless Channels: sirens, jellyfish and eels in darkness. Walk up to lanterns to light them and see far.
  - Floor 9, Flood Halls: eels, crabs, harpooners and sirens. The low halls flood and drain on a cycle.
  - Floor 10, Bottom of the Great Reservoir: all six types. You climb three sealed sections while the water rises behind you (XP that falls in floats up to you), then fight the Drowned Admiral and the Leviathan.
- Elites and bosses drop **relics**, locked items that match their family.
- Coins and relics you pick up are only kept when you step into the exit portal after the boss. If you die you go back to base and lose only what you had not secured.
- Each portal you reach lets you start from the next floor later (fast travel).
- The dodge roll (Space or the roll button) makes you untouchable for a moment and breaks webs.
- Each theme has one point budget for the loadout: 5 points for Mossy Ruins, 3 for Flooded Aqueducts.

## Sound

All sound effects and music are synthesized in the browser with the Web Audio API (no audio files). Each stage has its own generated song, and boss fights switch to a more intense version. Volumes for master, music and sound effects are in Settings (title screen, the gear button, or the pause menu) and are saved in the browser.

## Adding content

All content lives in the data tables at the top of the script in `index.html`:

- `ITEMS`: abilities, positive traits and negative traits (cost, rarity, text)
- `COMBOS`: item pairs with cost changes and effects
- `STAGES`: budget, rules, forced/banned items, enemy mix, rewards
- `ENEMIES` (behaviour stats) and `SKINS` (how each stage's enemies and bosses look; from stage 6 a skin's `ai` gives the enemy its own attack, and a boss's `patterns` list its attack cycle)
- `CHARACTERS`, `WEAPONS`
- `ICONS`: line icons as SVG paths, used in the menus and on the canvas
- Stage art lives in `TILES` (ground) and `PROPS` (scenery); enemy art in `DRAW`
- Dungeon: `DG_FLOORS` (each floor's ASCII map, boss, enemy packs and rewards; the map legend is in the comment at the top of the dungeon code), `DG_THEMES`, `DG_STAGES` (one loadout budget per theme), `DG_PACKS`, `DG_RELICS`, `DG_AI` (the aqueduct enemies' attacks), and `RIGS` (the blocky 3D models, one list of boxes per enemy or hero)

A new item's numbers go in `ITEMS`: `lv(L)` returns its stats at level L, and `show` lists which stats the level-up cards display. Its behavior goes in `recalcStats()` (passive traits) or `useAbility()` (active abilities).
