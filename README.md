# Loadout Survivors

A browser survivor-like game with a deckbuilding twist. Open `index.html` in any browser. No build step and no dependencies.

## Core idea

- Before each stage, build a **loadout** of abilities and traits.
- Abilities and positive traits **cost** points. Negative traits **give points back**.
- The total must fit the stage's **budget**. Unused points become bonus coins.
- Some pairs form **combos** that change costs or effects.
- Stages can **force** or **ban** traits.
- In a run, level-ups upgrade your weapon, abilities and positive traits, or **overcome** a negative trait.
- New items come from first clears and the **gacha**.
- Loadouts can be saved to 6 slots.

## Adding content

All content lives in the data tables at the top of the script in `index.html`:

- `ITEMS`: abilities, positive traits and negative traits (cost, rarity, text)
- `COMBOS`: item pairs with cost changes and effects
- `STAGES`: budget, rules, forced/banned items, enemy mix, rewards
- `ENEMIES`, `CHARACTERS`, `WEAPONS`

A new item's numbers go in `ITEMS`. Its behavior goes in `recalcStats()` (passive traits) or `useAbility()` (active abilities).
