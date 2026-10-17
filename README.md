# Loadout Survivors

A browser survivor-like dungeon crawler with a deckbuilding twist. Open `index.html` in any browser. No build step; the 3D view loads [Three.js](https://threejs.org) from a CDN when the page opens.

## Core idea

- Before each dive, build a **loadout** of abilities and traits.
- Abilities and positive traits **cost** points, shown as +n. Negative traits **give points back**, shown as −n.
- There are 15 abilities, 45 positive traits and 27 negative traits. The newer traits change how you play instead of adding to a stat, and several are modelled on the set effects of the L2 Artifacts Minecraft mod. For example:
  - **Sword of Damocles** (+3): damage +100% at full HP, but you die if your HP drops below 40%.
  - **Perfectionist** (+3): at full HP you build barriers that each block a whole hit.
  - **Cold Blood** (+5): no single hit can take more than 12% of your max HP, but every kill costs you a little HP.
  - **Ancient Scroll** (+4): keep moving for Wind (speed), stand still for Mountain (damage taken −50%).
  - Also Loaded Dice, Frostbite, Spore Burst, Gilded, Lone Marksman, Saint's Vow, Powder Keg, Riposte (perfect dodges) and Glutton.
  - The big, expensive ones: Overdrive (+6, every 18 s all cooldowns run 2.5× faster for 6 s), Titan (+6, barge through enemies), Phoenix (+5), Echo (+7, abilities fire twice), Midas Touch (+4, kills turn to gold) and Bullet Time (+6, rolls slow time).
  - Negative traits work the same way. Doomed (−6) sends an unkillable Death after you 75 s into each floor. Others are Lingering Wounds (hits shrink your max HP), Volatile Foes (enemies burst on death), Restless Dead (kills rise again) and Withered (healing −70%).
- The total must fit the **budget** of the theme you are diving into.
- The loadout you are building (character, items and starting floor) is saved, so it is still there when you reopen the game.
- Some pairs form **combos** (set bonuses) that change costs or effects, for example Perfect Edge (Damocles + Perfectionist), Bullet Dance (Bullet Time + Riposte) and Cheat Death (Phoenix + Doomed).
- In a run, each level-up is a small upgrade: weapon and abilities go to Lv 10, positive traits to Lv 8, and a negative trait can be **overcome** in 10 steps (Rigid and Grounded, which are either on or off, in one). Cards show the exact before → after numbers.
- A card is only offered when picking it changes something on this floor (`upgUseless`). For example, Second Wind is not offered once you have revived, when Last Breath already brings you back at full HP, or when Phoenix revives you instead; roll upgrades wait until Grounded is overcome; Heal is not offered at full HP.
- Runs have elites, treasure chests, drops (heal, magnet, bomb) and a kill chain that boosts XP.
- New items come from first clears, relic drops and the **gacha**. The first clear of floor 1 also unlocks the Knight.
- Loadouts can be saved to 6 slots.
- **Language**: English or Japanese (Settings → Language). The first time, the game follows the browser's language. The choice is kept apart from the save (`ls-lang` in local storage), so Reset save keeps it; changing it reloads the page, or applies the next time the game opens if a floor is in progress.
- Behind the title menu a real dungeon floor plays itself: a hero walks the map, fights whatever comes and moves on to another floor every 40 seconds (`tdemoFrame`). It cannot die, saves nothing and makes no sound. Without WebGL the old 2D demo shows instead.

The game used to have a second, 2D mode of ten timed survival stages. It was removed so everything happens in the dungeon; its last version is kept for reference in `reference/stages-2d.html` (it still plays on its own). The stages' first-clear items moved to the dungeon floors with the same number, and the 2D engine underneath (movement, weapons, enemies, level-ups) is what the dungeon runs on.

## The dungeon

Seen from behind and above, in the style of Minecraft Dungeons. It follows the dungeon design notes on floor structure, the death penalty, the first theme and enemy families:

- 100 floors in 20 themes of 5 floors are planned. Five themes are built: **Mossy Ruins** (floors 1–5), **Flooded Aqueducts** (floors 6–10), **Ember Mines** (floors 11–15), **Sky Archipelago** (floors 16–20) and **Candy Factory** (floors 21–25). The plan for all 100 floors is a shared document that is edited by hand.
- Every floor is a large hand-made map (about 7–10 minutes for a full clear) with its own floor boss. The last floor of a theme ends with the floor boss and then the theme boss, back to back.
- Enemy HP grows with the floor: the extra over floor 1 is doubled from the hand-set values (`DG_HP_GROW`), so ×2.4 on floor 5, ×3.5 on floor 10, ×4 on floor 15 and ×5.6 on floor 20. Bosses grow by the same factor, and from floor 11 on they have 2.5× that HP on top (`DG_BOSS_HP_DEEP`, the Forge King's arms included): 58,500 on floor 11, 84,000 on floor 15, 93,300 + 131,500 on floor 20.
- Each floor starts you back at level 1 with your bare loadout, and each level needs 1.1× the XP of the one before (`DG_XP_GROWTH`). XP gems stay where enemies fall.
- Every boss has several attacks of its own (for example the Broodmother's web shots, egg sacs, web field, pounce, venom spray and spiderlings); the attack list is `patterns` in its `SKINS` entry, and the attacks live in `bossStep()`. Bosses never throw just one thing: boulders, anchors, pearls, coral javelins and wreckage come in volleys, often bursting into shards where they land, and each thrown object has its own 3D model.
- Enemies get tougher the farther they are from the floor's start: from ×0.8 health and damage next to the start up to ×3 at the far end, rising evenly in between (`DG_DIST`). Far-away enemies are also bigger and give up to twice the XP.
- Floor 5 is a long sealed corridor: iron gates drop in front of you and behind you, and four waves attack in each section. There is no way back.
- Mossy Ruins gimmicks: spider webs slow you (floor 2), cracked floor falls away (floor 3), moss pools poison you (floor 4). Enemy families: Ruin Revenants (melee), Stone-eater Spiders (poison), Ruin Archers (ranged).
- In the Flooded Aqueducts every floor has a job to do before its boss gate unlocks (`objective` in each `DG_FLOORS` entry), its own map shape, its own light and colours (`look`, see `D3FLOOR`), and a signature enemy:
  - Floor 6, Sluice Gate: the Great Canal cuts the floor in two. Turn three sluice valves; each one sets off an ambush and drains part of the canal. Drowned Sailors grab you, Harpooners drag you in, and **Barnacle Cannons** grown into the walls fire bursts and lobbed shells across the crossings.
  - Floor 7, Crab Cisterns: nine raised cisterns in a flooded hall, joined by causeways. Break four **Brood Clutches** that keep hatching crabs while you are near. Ironshell Crabs are armored in front, and **Spinefish** swell up and burst into rings of spines.
  - Floor 8, Lightless Channels: a dark labyrinth. Light all six lanterns. **Lurking Anglers** wait at dead ends with lures that glow like lanterns, then lunge. Bubble Sirens, Electric Eels and Glow Jellyfish fill the channels.
  - Floor 9, Flood Halls: three great halls flood and drain on a cycle. Restart three pumps by staying next to each until it runs while eels and **Tide Wraiths** come out of the water; a running pump drains its hall for good.
  - Floor 10, Bottom of the Great Reservoir: a black lake of walkways and islands, and something enormous swims after anyone in the water (stay on the walkways). Board the Drowned Admiral's wreck and beat him on its deck; the reservoir then drains, the lair gates open, and the Leviathan waits in its pool. **Leviathan Spawn** leap out of the lake at you.
- The Ember Mines are an old dwarven mine over a sea of magma, and the hardest theme so far: tougher enemies (Kobold Miners in swarms, Kobold Sappers with dynamite, Magma Slimes that leave burning ground and split, Ember Bats that swoop, armored Rock Golems with shockwaves, Crystal Golems that burst into shards, blinking Forge Imps), higher floor multipliers and a 2-point loadout budget. Lava (`%`) blocks walking but not shots. Every floor has its own job (`objective`):
  - Floor 11, The Cart Line: escort the ore cart up a long rail line. It only rolls while you stay next to it and no enemy blocks it, and at three stations the kobolds attack it. Runaway carts race down the side tracks (`runaway` in the floor's entry) and hit anything in their way, kobolds included. Boss: Gearjaw on his drill cart.
  - Floor 12, Blasting Galleries: take dynamite from a crate, carry it (slower, and a hit knocks it out of your hands) to one of four rubble seams, and get clear before it blows. Boss: Blastback, the demolition golem.
  - Floor 13, Furnace Halls: a heat meter fills near furnaces and on hot grates; past 100 you burn until you cool off in a trough. Vent three furnaces by staying in their rings while the heat climbs. Boss: the Furnace Heart, which never moves and sweeps the room with turning flame jets.
  - Floor 14, Crystal Refinery: three emitters fire beams along the tile grid, and the beams burn anything crossing them. Walk up to a mirror to flip it between / and \, and bend each beam into its receiver (the puzzles are checked by a solver in the map script). Boss: the Prism Matriarch.
  - Floor 15, Heart of the Forge: the Forge King's two arms are separate enemies, and while either stands he takes 15% damage. Break them and the magma ring in his hall floods. When he falls the forge collapses: the magma follows you up the escape tunnel to the portal.
- The Sky Archipelago is open sky: floating islands at three heights (a height map per floor, `hmap`), plank bridges and airships, with nothing under the edges. Gusts push everyone (an arrow warns you first); walking enemies get blown off the edge and so can you (you fall, lose 12% HP and climb back where you last stood), and a dodge roll carries you over a short gap. Flying enemies cross the gaps. The theme forces Marked and bans Grounded and Heavy Feet. Enemies: Cloud Sprites (puff you around), Sky Pirates (leap across gaps), Pirate Gunners (aimed musket shots), Kite Riders (bombing runs), Deckhands (grappling hooks that drag you over the void), Storm Birds (dives that leave a crackling trail) and Sky Marines (a shield in front).
  - Floor 16, Island Hopping: wind bridges fade in and out on their own clocks; light three beacons. Boss: the Pirate Lookout.
  - Floor 17, Windmill Isles: a steady wind never stops. Climb the stairs to three hilltop windmills; each one you turn swings the wind its way. Boss: the Weathervane.
  - Floor 18, Airship Dock: climb a gangplank to each of three airships, cut its mooring post and get off before it casts off and flies away. Boss: the Dock Master.
  - Floor 19, Thunderhead: islands of cloud, and lightning lands on the beat of the music. Stand by a lightning rod when a bolt lands and the rod takes it instead of you; three bolts charge a rod. Boss: the Thunderhead.
  - Floor 20, The Flagship: escort ships fire broadsides across the deck in lanes; spike the four deck cannons to silence them. Then Admiral Skye, and then the Flagship itself.
- The Candy Factory is a giant sweets factory in pink, mint and chocolate. Its rule: sticky syrup slows you to ×0.55 (and every walking enemy), and sugar cubes (on the map, from popped gumdrops, sometimes from enemies) give a 5 s sugar rush of +40% speed. Volatile Foes is forced and Sluggish banned. Enemies: Gummy Bears (squash, then spring onto you; they split into two cubs), Jelly Cubes (leave sticky jelly, slam), Marshmallows (roll at you, burst into goo), Choco Crocs (under the chocolate they take 75% less damage, then surface and snap), Lollipop Witches (spinning rings of candy), Cookie Soldiers (a shield in front, cookies that bounce off walls), Candy Wasps (dart through you, the sting slows) and Frosting Knights (shield, a lance charge that leaves frosting). The maps use letters only these floors know (`maps_candy.py`; the candy parser in `part27_candy.js` turns them into tiles and flags).
  - Floor 21, Gumdrop Gardens: gumdrop clusters block shortcuts and side rooms. They are fixed enemies that block their tile, so every weapon pops them (auto-aim picks real enemies first). Pop the three giant gumdrops to unseal the gate. Boss: the Gumdrop Golem, whose slam walls you in with new gumdrops.
  - Floor 22, Chocolate River: hot chocolate loops around the middle island; wading is ×0.45 speed and burns 1.5% of your HP every half second. Wafer rafts ride the loop and carry whoever stands on them. Ring three dock bells on the banks. Boss: the Chocolatier.
  - Floor 23, Conveyor Kitchen: belts push everyone (you at 150, enemies at 200 px/s) and end in ovens that burn whatever rides in. Pull the lever by each stopped belt line to send its giant cake into the oven; bake all three. Boss: the Head Baker, who can run every belt backwards.
  - Floor 24, Cotton Candy Clouds: cotton candy islands with heights over a pink sky (you can fall). A bounce pad throws you the way you are walking, over the gap to the first ground beyond (up to 12 tiles); the three sprinkle jars sit on islets only a pad reaches. Boss: Queen Bee-Bonbon.
  - Floor 25, The Wedding Cake: five tiers, each ringed by a frosting rim, with the stairs up on alternating sides. Light the candle on tiers one to four. Then the Sugar Tyrant, and then the Cake itself.
- Height floors are flagged by `D3.hi` (the Sky, and floors 24–25): the height map, cliffs and clouds below come from the Sky's code, with candy materials swapped in.
- From floor 20 on, enemies can carry **traits** of their own, modelled on the L2 Hostility mod (`HTRAITS`): Fiery, Invisible, Protection, Regenerate, Speedy, Tank, Seeker, Gravity, Counter Strike, Adaptive, Reflect, Grenade, Split, Drain, Blinder, Stray, Poisonous, and for elites and bosses Dementor, Teleport, Repelling, Pulling, Undying and Killer Aura. Some normal enemies get one, elites always get one or two, and bosses at least one. Their names float over them and sit next to the boss's name.
- **Elites** are much tougher than normal enemies (about 12.6× HP and +1 armor), and each enemy type's elite has one special attack of its own, named in the floor brief and shouted when it is used: for example the Revenant's Grave Slam (a leap onto you), the Siren's Siren Song (drags you in), the Sapper's Bundle Charge (lobbed dynamite) and the Kobold Miner's Foreman's Call (calls more kobolds). Every special shows a warning first. The data is `ELITE_SP`.
- Elites and bosses drop **relics**, locked items that match their family.
- Coins and relics you pick up are only kept when you step into the exit portal after the boss. If you die you go back to base and lose only what you had not secured.
- Each portal you reach lets you start from the next floor later (fast travel).
- First-clear rewards are also handed over when the game loads for floors a save had already cleared before the reward existed (for example the Knight on floor 1, which was a 2D-stage reward before the stages were removed).
- The dodge roll (Space or the roll button) makes you untouchable for a moment and breaks webs. You move in steps of at most 8 px with a wall check after each (a roll covers up to 35 px in a frame, and used to slip through one-tile walls and closed gates).
- On a touch screen, a long press selects no text, and swipes never reach the browser (some in-app browsers closed the page on a downward swipe), except where a list can still scroll.
- Each theme has one point budget for the loadout: 5 points for Mossy Ruins, 3 for Flooded Aqueducts, 2 for the Ember Mines and the Sky Archipelago. A theme can also force or ban items (the Sky Archipelago forces Marked and bans Grounded and Heavy Feet).
- **The Forge** (the Forge tab) turns coins into permanent upgrades (`part25_forge.js` in the build, saved as `save.forge`). It opens once floor 10 is cleared; until then its tab shows a lock and what it will offer, and when it opens the tab says NEW:
  - Abilities and positive traits get stars, ★1 to ★5, for 200 / 400 / 800 / 1600 / 3200 coins (6200 for all five). Each star makes every number of the item 10% stronger, what each level-up adds included: damage, radius, chances and bonuses ×1.1 per star, cooldowns and other lower-is-better numbers ÷1.1. Counts (blades, fireballs, drones, chain jumps, barriers) stay whole and do not change, and a few numbers are capped so they stay sane (for example Haste and Bulwark at 60%, Second Wind at 100%). Forged items show their stars in the loadout.
  - Characters can be upgraded 30 times, each +5% max HP and +5% damage. Upgrade k costs 100 + 50·k·(k − 1): 100, 200, 400, 700, 1100, 1600, 2200 … 43,600 for the 30th (452,500 in all).
  - Negative traits cannot be forged.
- **Skill tree** (the Skills tab): the first clear of a floor gives 1 skill point, and every fifth floor gives 2. Each rank of a skill costs one point more than the rank before it (1, then 2, then 3 …); the three capstones cost a flat 3. Three branches: Agility (roll cooldown, roll distance, move speed, longer i-frames, Tailwind, Water Strider, and Double Roll at the end), Might (damage, cooldowns, crits, Giant Slayer, Head Start, XP, and Overkill) and Survival (max HP, armor, pickup range, a Healing Flask, coins, Lantern Bearer, and Last Stand). A branch's middle row opens after 4 points in its first row, its last row after 10. You can take points back out at any time. Skills only work in the dungeon; the data is `DG_SKILLS`.

## Performance

- The static level is split into chunks of 10×10 tiles (`d3Instanced`), so the camera and the shadow pass only draw what is near the view, and nothing past the fog is drawn.
- Only five point lights exist: four for the torches nearest the camera (an open portal borrows one) and one on the player. The count never changes during play, so materials are never recompiled mid-run. Shadows use plain PCF filtering.
- Phones skip MSAA.
- Screens faster than about 100 Hz (120 Hz phones) are drawn at 60 fps, every other refresh, instead of doing all the work twice as often.
- Less garbage per frame, so fewer garbage-collection pauses: the per-frame lists are cleaned in place (`compact`), character colours are kept per rig and skin, and one animation-state object is reused.
- The minimap only paints newly seen tiles; the whole map is repainted only when tiles change. The enemies' path map (`dgFlow`) reaches 48 tiles around you instead of the whole floor.
- Every material's shader (shadow pass included) is built, and its texture uploaded, while a floor loads (`d3Warm`). Before, lava and water built theirs the first time they came into view, which froze phones for a moment and made the music skip (most noticeable on floor 13).
- The ground layer (warning circles, pools, rings) is a canvas re-uploaded every frame, so it is kept at 768 px (512 on phones). Magma slimes only leave trails near you, lava glows once per 2×2 block, and when Auto has already lowered the resolution, big glows shrink too.
- Settings → Graphics cycles through three modes. **Auto** (default) lowers the resolution while the game runs under about 48 fps and raises it again once there is room (`d3AutoRes`). Every change resizes the drawing buffer, which freezes the game for 50–100 ms, so it never changes in the middle of play: a step down (8–20% per side, never below 60%) is decided after 1.5 s under 48 fps and applied at the next pause (level-up cards, pause menu) or floor load. Only when the game is really struggling (6 s under 30 fps with no pause) does it change on the spot, in one bigger step (up to 30%). A step up (×1.15) is also only tried at a pause or floor load, after 6 s at 54 fps or better; one that does not hold goes back down and is not tried again on that floor. **Sharp** always draws at full resolution. **Fast** always draws at 70% resolution without shadows.
- A resize reallocates the drawing buffer once (`setDrawingBufferSize`; `setPixelRatio` then `setSize` did it twice) and not at all when the size is unchanged, which phones' sliding browser bars used to trigger.
- When the level is rebuilt in the middle of a floor (drained canals, blasted seams), the shader warm-up and the resolution step are skipped: they belong to floor loads.
- The HUD (HP, XP, level, kills, coins, objective, roll and flask buttons) is refreshed every 80 ms, but each piece is written only when its value changes (`hudTx`, `hudHt`, `hudW`). Rewriting it all every time made the browser lay out the page again about 12 times a second: a few ms each time, 8–10 ms with Japanese text, and a skipped frame on phones.
- Nothing is left running once its job is done. Long tests (10+ minutes, floors 1–6, 11–16, and level 28 on one floor) show a flat heap, frame cost and 3D object count. Every sound effect and music note unplugs its chain of audio nodes when it ends (`audFree`): Safari otherwise kept them in the audio graph, about 80 a second, and a long session got slower until a reload.
- While the game is paused, the 3D view still draws but adds no particles (furnace sparks, steam and the like used to pile up and fly off together when play resumed).

## Sound

All sound effects and music are synthesized in the browser with the Web Audio API (no audio files). The music is hand-written: each song in `SONGS` lists its chords bar by bar, its melodies as note:length pairs and its drum grooves, and plays an intro and then a form like A A+ B B+ A~ A+ (`+` adds an arpeggio, a harmony line and busier drums, `~` is a breather). Everything runs through a small mixer with reverb and a tempo-synced echo. There are songs for the title, the base, each theme, the floor bosses of each theme, and a finale for the theme bosses on floors 5, 10, 15 and 20. The Sky Archipelago's song runs at 120 bpm, so floor 19's lightning lands on its beat. Pausing or a level-up muffles the music. Volumes for master, music and sound effects are in Settings (title screen, the gear button, or the pause menu) and are saved in the browser.

## Translation

The game is written in English and translated where text reaches the screen (`part26_i18n.js` in the build), so the game code itself never changes with the language:

- DOM text, tooltips, labels and placeholders go through a MutationObserver; canvas text (banners, floating text, names over heads, boss attack names) through `fillText` / `strokeText`.
- `tr()` looks a string up in `I18N_JA`: first exactly, then with its numbers replaced by `{0}`, `{1}` … (so "Max HP −25%" and "Max HP −40%" share one entry), then as an ALL-CAPS version of an entry, then through the patterns in `I18N_JA_RX` for text with a name inside ("Overcome Fragile", "Moss Sentinel awakens"), then part by part around " · ". Text that is already Japanese is left alone. Results are cached.
- The dictionary (about 1,550 entries) was built by running the game with an empty dictionary in a collector mode (`I18N_COLLECT`, which records every string that reaches the screen) through every hub screen, all 20 floors, their bosses, elites, level-ups, pauses, deaths and clears, plus a sweep of every banner, toast and floating text in the code. Proper names come from one glossary so they read the same everywhere.
- New text needs a Japanese entry, or it shows in English. Run the collector to find what is missing.

## Adding content

All content lives in the data tables at the top of the script in `index.html`:

- `ITEMS`: abilities, positive traits and negative traits (cost, rarity, text)
- `COMBOS`: item pairs with cost changes and effects
- `ENEMIES` (behaviour stats) and `SKINS` (how enemies and bosses look; a skin's `ai` gives an enemy its own attack, and a boss's `patterns` list its attack cycle)
- `CHARACTERS`, `WEAPONS`
- `ICONS`: line icons as SVG paths, used in the menus and on the canvas
- `SONGS` (the music), `SFX` (sound effects)
- `STAGES`, `TILES`, `PROPS` and `DRAW` are left from the 2D mode; they now only draw the base menu's background and the title's fallback when 3D is not available
- Dungeon: `DG_FLOORS` (each floor's ASCII map, boss, enemy packs, objective and first-clear rewards: coins, items and characters; the map legend is in the comment at the top of the dungeon code), `DG_THEMES`, `DG_STAGES` (one loadout budget per theme), `DG_PACKS`, `DG_RELICS`, `DG_AI` (the aqueduct enemies' attacks), `DG_SKILLS` (the skill tree), `D3FLOOR` (per-floor looks), and `RIGS` (the blocky 3D models, one list of boxes per enemy or hero)

A new item's numbers go in `ITEMS`: `lv(L)` returns its stats at level L, and `show` lists which stats the level-up cards display. Its behavior goes in `recalcStats()` (stat traits), `useAbility()` (active abilities), or the trait hooks at the end of the items section (`traitsTick` runs every frame; `hitEnemy`, `killEnemy`, `hurtPlayer` and `dgRoll` are wrapped there).
