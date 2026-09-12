# Prestige — Forge 1.12.2 mod

Adds a single command, `/prestige`, for the `naruto server 2026` pack.
Built against Forge `14.23.5.2859` for Minecraft `1.12.2`.
Modid: `prestige`. Package: `com.prestige.mod`.

## What it does

A player runs `/prestige`. If their current NXP meets the requirement for
their next tier, they prestige:
- NXP resets to 0 (via `/addninxp`)
- Their rank bumps up (via `/rank add`), raising their enforced NXP cap
- Any previously-granted kekkei genkai/releases are wiped, then they're
  granted exactly one new one for this tier (via `/giveninjarelease`) — 12
  tiers total, Rinnegan/Tenseigan excluded
- Any configured "release" items get cleared from their inventory
- Their prestige count (tracked on a scoreboard) goes up by 1

If they're not at the required NXP yet, it just tells them how much more
they need — nothing happens.

Tier thresholds (staggered, ends at 250,000 NXP):

| Prestige | NXP required |
|---|---|
| 1 | 5,000 |
| 2 | 10,000 |
| 3 | 20,000 |
| 4 | 50,000 |
| 5 | 90,000 |
| 6 | 130,000 |
| 7 | 170,000 |
| 8 | 200,000 |
| 9 | 220,000 |
| 10 | 235,000 |
| 11 | 245,000 |
| 12 | 250,000 |

## Building it

1. If you already have a working MDK folder from before, just copy the new
   `src` folder from this project over the old one, replacing the old
   `com/playerscroll` package — delete the leftover `com/playerscroll` folder
   in your MDK's `src` if it's still sitting there after copying, since the
   mod now lives under `com/prestige` instead.
2. In your MDK's `build.gradle`, update the same 3 lines you edited before:
   `version = '1.0.0'`, `group = 'com.prestige.mod'`,
   `archivesBaseName = 'prestige'`.
3. Run `.\gradlew.bat clean build` (Windows) or `./gradlew clean build`
   (Mac/Linux).
4. Grab `prestige-1.0.0.jar` from `build/libs/`.
5. Remove the old `playerscroll-1.0.0.jar` from every `mods/` folder and
   replace it with this one (server and all clients).

## Before this works in-game

1. **Run every line in `one_time_setup_commands.txt` once**, in console —
   creates the 12 ranks and the `prestige` scoreboard objective (skip this if
   you already ran it before, it's unchanged).
2. **Fill in `NXP_TAG_PATH`** in `CommandPrestige.java` — find it using
   NBTExplorer on your own player `.dat` file (look under `ForgeCaps`), then
   rebuild. Without this, `/prestige` can't read anyone's current NXP.
3. **Fill in `RELEASE_TAG_PATHS_TO_CLEAR`** in the same file — while you're
   already in NBTExplorer for step 2, look in the same capability compound
   for whatever field(s) track which kekkei genkai/releases a player has
   unlocked, and list their full paths. There's no native command to remove
   a granted release, so this works by deleting those tags directly from the
   player's saved data, which makes the mod fall back to its own default
   (almost always "not unlocked") the next time it reads that field.
   **This directly edits another mod's save data, so test it on a throwaway
   test character first** before trusting it on real players — if the field
   turns out to hold something other than a simple flag/list, deleting it
   could behave unexpectedly.
4. **Fill in `RELEASE_ITEMS_TO_CLEAR`** with the actual item registry names
   (e.g. `ahznbnarutomod:water_release_scroll`) of anything that should be
   wiped from inventory on prestige. Leave it empty to skip that step.
5. Confirm the 12 kekkei genkai names (`Ice`, `Lava`, `Boil`, `Explosion`,
   `Magnet`, `Scorch`, `Storm`, `Wood`, `Particle`, `Shikotsumyaku`,
   `Sharingan`, `Byakugan`) are what `/giveninjarelease` actually accepts.

Send me updates on any of these and I'll patch the source directly.
