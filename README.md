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
- Every boss has several attacks of its own (for example the Broodmother's web shots, egg sacs, web field, pounce, venom spray and spiderlings); the attack list is `patterns` in its `SKINS` entry, and the attacks live in `bossStep()`. Bosses never throw just one thing: boulders, anchors, pearls, coral javelins and wreckage come in volleys, often bursting into shards where they land, and each thrown object has its own 3D model.
- Enemies get tougher the farther they are from the floor's start: from ×0.8 health and damage next to the start to ×1.35 at the far end (`DG_DIST`). Far-away enemies are also a little bigger and give more XP.
- Floor 5 is a long sealed corridor: iron gates drop in front of you and behind you, and four waves attack in each section. There is no way back.
- Mossy Ruins gimmicks: spider webs slow you (floor 2), cracked floor falls away (floor 3), moss pools poison you (floor 4). Enemy families: Ruin Revenants (melee), Stone-eater Spiders (poison), Ruin Archers (ranged).
- In the Flooded Aqueducts every floor has a job to do before its boss gate unlocks (`objective` in each `DG_FLOORS` entry), its own map shape, its own light and colours (`look`, see `D3FLOOR`), and a signature enemy:
  - Floor 6, Sluice Gate: the Great Canal cuts the floor in two. Turn three sluice valves; each one sets off an ambush and drains part of the canal. Drowned Sailors grab you, Harpooners drag you in, and **Barnacle Cannons** grown into the walls fire bursts and lobbed shells across the crossings.
  - Floor 7, Crab Cisterns: nine raised cisterns in a flooded hall, joined by causeways. Break four **Brood Clutches** that keep hatching crabs while you are near. Ironshell Crabs are armored in front, and **Spinefish** swell up and burst into rings of spines.
  - Floor 8, Lightless Channels: a dark labyrinth. Light all six lanterns. **Lurking Anglers** wait at dead ends with lures that glow like lanterns, then lunge. Bubble Sirens, Electric Eels and Glow Jellyfish fill the channels.
  - Floor 9, Flood Halls: three great halls flood and drain on a cycle. Restart three pumps by staying next to each until it runs while eels and **Tide Wraiths** come out of the water; a running pump drains its hall for good.
  - Floor 10, Bottom of the Great Reservoir: a black lake of walkways and islands, and something enormous swims after anyone in the water (stay on the walkways). Board the Drowned Admiral's wreck and beat him on its deck; the reservoir then drains, the lair gates open, and the Leviathan waits in its pool. **Leviathan Spawn** leap out of the lake at you.
- Elites and bosses drop **relics**, locked items that match their family.
- Coins and relics you pick up are only kept when you step into the exit portal after the boss. If you die you go back to base and lose only what you had not secured.
- Each portal you reach lets you start from the next floor later (fast travel).
- The dodge roll (Space or the roll button) makes you untouchable for a moment and breaks webs.
- Each theme has one point budget for the loadout: 5 points for Mossy Ruins, 3 for Flooded Aqueducts.
- **Skill tree** (the Skills tab): the first clear of a floor gives 1 skill point, and every fifth floor gives 2. Three branches: Agility (roll cooldown, roll distance, move speed, longer i-frames, Tailwind, Water Strider, and Double Roll at the end), Might (damage, cooldowns, crits, Giant Slayer, Head Start, XP, and Overkill) and Survival (max HP, armor, pickup range, a Healing Flask, coins, Lantern Bearer, and Last Stand). A branch's middle row opens after 4 points in its first row, its last row after 10. You can take points back out at any time. Skills only work in the dungeon; the data is `DG_SKILLS`.

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
- Dungeon: `DG_FLOORS` (each floor's ASCII map, boss, enemy packs, objective and rewards; the map legend is in the comment at the top of the dungeon code), `DG_THEMES`, `DG_STAGES` (one loadout budget per theme), `DG_PACKS`, `DG_RELICS`, `DG_AI` (the aqueduct enemies' attacks), `DG_SKILLS` (the skill tree), `D3FLOOR` (per-floor looks), and `RIGS` (the blocky 3D models, one list of boxes per enemy or hero)

A new item's numbers go in `ITEMS`: `lv(L)` returns its stats at level L, and `show` lists which stats the level-up cards display. Its behavior goes in `recalcStats()` (passive traits) or `useAbility()` (active abilities).
