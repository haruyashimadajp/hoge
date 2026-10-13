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

- 100 floors in 20 themes of 5 floors are planned. Theme 1, **Mossy Ruins** (floors 1–5), is built.
- Every floor is a large hand-made map (about 6–8 minutes for a full clear) with its own floor boss. Floor 5 ends with the floor boss and then the theme boss, back to back.
- Each floor starts you back at level 1 with your bare loadout, and each level needs 1.1× the XP of the one before (`DG_XP_GROWTH`; the stages use 1.08×). XP gems stay where enemies fall.
- Every boss has several attacks of its own (for example the Broodmother's web shots, egg sacs, web field, pounce, venom spray and spiderlings); the attack list is `patterns` in its `SKINS` entry, and the attacks live in `bossStep()`.
- Floor 5 is a long sealed corridor: iron gates drop in front of you and behind you, and four waves attack in each section. There is no way back.
- Floor gimmicks: spider webs slow you (floor 2), cracked floor falls away (floor 3), moss pools poison you (floor 4).
- Enemy families: Ruin Revenants (melee), Stone-eater Spiders (poison), Ruin Archers (ranged). Elites and bosses drop **relics**, locked items that match their family.
- Coins and relics you pick up are only kept when you step into the exit portal after the boss. If you die you go back to base and lose only what you had not secured.
- Each portal you reach lets you start from the next floor later (fast travel).
- The dodge roll (Space or the roll button) makes you untouchable for a moment and breaks webs.
- The whole theme has one point budget for the loadout.

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
- Dungeon: `DG_FLOORS` (each floor's ASCII map, boss, enemy pressure and rewards; the legend is in the comment above it), `DG_THEMES`, `DG_PACKS`, `DG_RELICS`, and `RIGS` (the blocky 3D models, one list of boxes per enemy or hero)

A new item's numbers go in `ITEMS`: `lv(L)` returns its stats at level L, and `show` lists which stats the level-up cards display. Its behavior goes in `recalcStats()` (passive traits) or `useAbility()` (active abilities).
