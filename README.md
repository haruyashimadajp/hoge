# Loadout Survivors

A browser survivor-like game with a deckbuilding twist. Open `index.html` in any browser. No build step and no dependencies.

## Core idea

- Before each stage, build a **loadout** of abilities and traits.
- Abilities and positive traits **cost** points. Negative traits **give points back**.
- The total must fit the stage's **budget**. Unused points become bonus coins.
- Some pairs form **combos** that change costs or effects.
- Stages can **force** or **ban** traits.
- In a run, each level-up is a small upgrade: weapon and abilities go to Lv 10, positive traits to Lv 8, and a negative trait can be **overcome** in 4 steps. Cards show the exact before → after numbers.
- Runs have elites, swarms, treasure chests, drops (heal, magnet, bomb) and a kill chain that boosts XP.
- New items come from first clears and the **gacha**.
- Loadouts can be saved to 6 slots.

## Adding content

All content lives in the data tables at the top of the script in `index.html`:

- `ITEMS`: abilities, positive traits and negative traits (cost, rarity, text)
- `COMBOS`: item pairs with cost changes and effects
- `STAGES`: budget, rules, forced/banned items, enemy mix, rewards
- `ENEMIES`, `CHARACTERS`, `WEAPONS`

A new item's numbers go in `ITEMS`: `lv(L)` returns its stats at level L, and `show` lists which stats the level-up cards display. Its behavior goes in `recalcStats()` (passive traits) or `useAbility()` (active abilities).
