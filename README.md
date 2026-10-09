# VEXT-Zombies

An infection-style "zombies" game mode for Battlefield 3, written in Lua as a
[Venice Unleashed](https://veniceunleashed.net) (VU) extension. Players start as
humans (team 1). Once enough players are present one is picked as the first
zombie (team 2), and any human killed by a zombie is moved to the zombie team.
Humans win by surviving the round timer; zombies win by infecting everyone.

**This is an unfinished work in progress.** It dates from the early VEXT
scripting API (2016) and has not been updated for the current Venice Unleashed
mod format, so expect it to need porting before it runs on a modern VU server.

## What is implemented

Server (`ext/Server`)
- Pre-game: every 2 seconds all players are forced onto the human team (players
  on other teams are killed and moved). When the human team has more than one
  player, the round starts and a random player is made the first zombie.
- Infection: a human killed by a zombie is moved to the zombie team, with a chat
  announcement.
- Round end: the round lasts 5 minutes (`m_RoundTime = 300` in
  `ZombiesLogic.lua`), or ends early if no humans are left. The result is
  announced in chat.

Shared (`ext/Shared`), applied to game data as it loads
- Loadouts: the zombie team (RU kits) loses primary, secondary and gadget
  slots; the human team (US kits) loses gadgets. Both lose M67 grenades and
  medic bags. Humans get only "no specialization"; zombies get only Sprint
  Boost L2 (its value raised to 1.5).
- Soldiers: max health 200, no spawn protection, no interactive man-down
  state, dropped kits cannot be picked up, heal speed boost changed to 0.6,
  every scoring event worth 420 points.

Client (`ext/Client`)
- Visual environment overrides for a dark, foggy night look (sky, outdoor
  light, fog, tonemap, colour correction, random wind strength).
- Brighter, shadow-casting spot and point lights, including the flashlight.
- A separate zombie-team visual preset exists (`SetZombieVisuals`) but is not
  currently used; everyone gets the human preset.

## Requirements

- A Venice Unleashed (Battlefield 3) server.
- The mod has no configuration file, web UI or console commands; values such as
  round length are set directly in the Lua sources.

## Installation

1. Copy the repository folder into your server's `Admin/Mods/` directory, e.g.
   `Admin/Mods/zombies/`.
2. Add the mod's folder name to `Admin/ModList.txt`.
3. Restart the server.

The mod manifest is `zombies.json` (old VEXT format, `HasVeniceEXT: true`).
Current Venice Unleashed releases expect a `mod.json` manifest and a newer
scripting API, so the manifest and some API calls will likely need updating.

## Structure

```
zombies.json                    Mod manifest (old VEXT format)
ext/Server/__init__.lua         Server entry point, update loop
ext/Server/ZombiesLogic.lua     Pre-game, round timer, game over
ext/Server/ZombiesTeamManager.lua  Team setup, zombie selection, infection
ext/Shared/SharedUnlocks.lua    Per-team loadout restrictions
ext/Shared/SharedSpecials.lua   Soldier and gameplay tweaks
ext/Shared/Logger.lua           Console logging helper
ext/Client/ZombiesVisuals.lua   Night/fog visuals and lighting
```

## Status and known issues

- WIP and unmaintained.
- The damage-scaling hook (`Soldier:DamageSimple`, scaling human damage by the
  zombie/human ratio) is disabled because it crashed VU at the time.
- The per-minute "time left" announcement and random zombie selection have
  rough edges (float timer checks, 0-based random index against 1-based
  player list).

## License

MIT, see [LICENSE](LICENSE).
