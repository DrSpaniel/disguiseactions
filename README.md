# DisguiseActions

A LibsDisguises companion plugin that turns mob disguises into playable kits: action
hotbars, mob movement speeds, a creeper bomb with a smart escape, and a prank-grade
survivability suite.

## What it does

**Mob action bars.** Disguise as a mob with defined actions and your 9 hotbar slots are
safely stashed away, replaced with that mob's action items. Undisguise (or log out, or
die) and your exact hotbar comes back. Action items are locked in place — no moving,
dragging, dropping, or offhand-swapping them — and your real items never leak into
death drops. Mobs *without* actions keep your normal hotbar untouched, so a skeleton
disguise can still hold and fire a bow.

**Creeper bomb.** The creeper kit gives you two books:

- **Ignite Fuse** — right-click to prime yourself. An invisible, AI-less, invulnerable
  creeper ignites at your feet with a real fuse, your own disguise flashes white in
  sync with the genuine hiss, and the detonation is a real vanilla explosion that
  damages terrain (and respects `mobGriefing`). Configurable fuse length and cooldown.
- **Poof** — right-click to instantly drop the disguise.

Once lit, the fuse is committed: undisguising or logging out mid-fuse doesn't stop the
explosion.

**Smart escape.** One tick before detonation, the plugin picks your getaway: it samples
dozens of candidate spots in a radius around the blast, keeps only safe landings, and
— by default — chooses somewhere no player has line of sight to (behind walls, around
corners), falling back to the spot furthest from the nearest player. You teleport
there silently — no sound, no particles — and undisguise in the same tick, so witnesses
see a creeper vanish and a crater appear, never a player. Optionally snap your camera
to face the explosion on landing ("watch it burn" mode).

**Decoy escape.** Take a hit that would drop you to/below a configurable health
threshold while creeper-disguised and the hit is cancelled: you escape with the smart
teleport, and a *real* creeper spawns in your exact spot and takes the original hit —
genuine hurt flash, knockback, and sound. Your attacker thinks they just killed a
regular creeper.

**Silent regen.** Optional heal-over-time while creeper-disguised. Heals directly with
no potion effect, so there are no particles, no sound, nothing visible.

**Death message suppression.** Die while creeper-disguised and nothing hits the chat.

**Mob speed matching.** Every disguise sets your walk speed to that mob's normal
walking speed (creepers shamble, spiders skitter), restored on undisguise. Tunable per
mob in config; sprinting still multiplies on top like a speed potion.

**Escape test markers.** Place the configured marker block (default: barrier, changeable)
where a "victim" would stand and the escape algorithm treats it as a virtual observer
— line-of-sight and distance both count. Test the getaway logic solo, no second player
needed.

**Live config.** `/disguiseactions` views and changes every setting in-game with no
restart; changes save straight to `config.yml`.

## Requirements

- Paper or Purpur 26.3 (Java 25)
- LibsDisguises (and its PacketEvents dependency)

## Installation

1. Drop `disguiseactions-<version>.jar` into your server's `plugins/` folder, next to
   LibsDisguises.
2. Restart the server. The default `config.yml` generates on first run.
3. Disguise as a creeper (through LibsDisguises as usual) and the action bar appears.

## Usage

Disguise as a creeper, walk up to your victim, right-click the **Ignite Fuse** book,
and enjoy the show. Right-click **Poof** any time to drop the disguise instantly.

Tip for testing the escape alone: place a barrier block where your victim would stand,
ignite, and check that you land somewhere the "victim" can't see.

## Commands

`/disguiseactions` — view and change settings live (permission `disguiseactions.admin`).

- `/disguiseactions` — list all settings with current values
- `/disguiseactions <setting>` — show one value
- `/disguiseactions <setting> <value>` — set and save immediately
- `/disguiseactions reload` — reload `config.yml` from disk

Settings: `creeper`, `fuse`, `cooldown`, `escape-radius`, `escape-samples`,
`escape-range-y`, `face-explosion`, `break-line-of-sight`, `los-range`, `speed`,
`decoy`, `regen`. Tab-completes names and `true`/`false`.

## Permissions

| Permission | Default | Description |
|---|---|---|
| `disguiseactions.use` | op | Action bars and mob speed matching |
| `disguiseactions.admin` | op | View/change settings via `/disguiseactions` |

## Configuration

```yaml
actions:
  creeper:
    enabled: true
    fuse-ticks: 30          # 20 ticks = 1 second; vanilla creeper fuse
    cooldown-seconds: 5
    escape:
      radius: 16            # how far the escape teleport may go
      samples: 48           # candidate spots evaluated per escape
      range-y: 8            # vertical scan range for safe landings
      break-line-of-sight: true   # escape where no one can see you...
      face-explosion: false       # ...or face the blast ("watch it burn" mode)
      los-check-range: 48   # only players within this range count
      test-markers:
        enabled: true       # barrier blocks act as virtual observers
        material: BARRIER   # changeable to any creative-only block
    items:
      ignite:
        material: BOOK
        name: "&cIgnite Fuse"
      undisguise:
        material: BOOK
        name: "&7Poof"

movement-speed:
  enabled: true
  overrides: {}             # per-mob walk-speed tuning, e.g. CREEPER: 0.12

survivability:
  hide-death-message: true  # no chat broadcast if you die creeper-disguised
  regen:
    enabled: false          # silent heal-over-time while creeper-disguised
    amount: 1.0             # half-hearts per interval
    interval-ticks: 100     # 5 seconds
  decoy:
    enabled: true           # escape + leave a real creeper to take the hit
    trigger-health: 0.0     # fire when a hit would take you to/below this
    decoy-health: 6.0       # the replacement creeper's health
```

## How it works (technical notes)

- Hooks LibsDisguises' `DisguiseEvent` / `UndisguiseEvent`; hard-depends on it in
  `plugin.yml`.
- The fuse creeper is a real ignited `Creeper` entity (invisible, no AI,
  invulnerable) — the hiss, flash timing, and explosion are 100% vanilla behavior,
  which is why it's convincing.
- Your disguise's white flashing is the creeper watcher's real ignited flag, so
  observers see exactly what a primed creeper looks like.
- Bukkit's creeper fuse counter counts *up* (vanilla swell, 0 → maxSwell); the escape
  fires at `getFuseTicks() >= getMaxFuseTicks() - 1`.
- Escape candidates are sampled uniformly over a disc, filtered for safe footing,
  then scored by observer visibility (raytraced) and distance.
- Death drops are edited directly because Bukkit snapshots them before handlers run.

## Building from source

`./build.sh` compiles with the local JDK 25 against `paper-api-26.3` and the
LibsDisguises API jar (API signatures verified with `javap` before compiling).
The LibsDisguises creeper-watcher call is reflective so PacketEvents isn't needed on
the compile classpath.

## Roadmap

The action system is a registry keyed by disguise type — new mob kits (skeleton
volley, enderman blink, …) plug in without touching the core.
