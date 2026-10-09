# DisguiseActions

A LibsDisguises companion plugin that turns mob disguises into playable kits: hotbar action triggers, mob movement speeds, a creeper bomb with a smart escape, and a prank-grade survivability suite.

## What it does

**Creeper action triggers.** Disguise as a creeper and your 9 hotbar slots are safely stashed away and cleared. While disguised, press **F** (swap hands) on a hotbar slot to trigger its action:

- **Slot 1 — Poof.** Instantly drop the disguise.
- **Slot 2 — Ignite.** Prime the creeper bomb (see below).

The F press is consumed, so the hotbar stays empty and locked — nothing can be clicked, dragged, shift-clicked, number-key swapped, or picked up into it while disguised, and nothing you own can be lost. Undisguise (or log out, or die) and your exact hotbar comes back, including into your death drops. A hint sidebar appears only while you're disguised, teaching the triggers; your previous scoreboard is restored exactly on undisguise. Mobs *without* actions keep your normal hotbar untouched, so a skeleton disguise can still hold and fire a bow.

**Creeper bomb.** Press F on slot 2 to prime yourself:

1. An invisible, AI-less, invulnerable creeper ignites at your feet with a real fuse — genuine hiss included.
2. Your own disguise flashes white in sync, like a real primed creeper.
3. The detonation is a real vanilla explosion that damages terrain (and respects `mobGriefing`).

Configurable fuse length and cooldown. Once lit, the fuse is committed: undisguising or logging out mid-fuse doesn't stop the explosion.

**Smart escape.** One tick before detonation (or the instant you ignite, with `tponexplode`), the plugin picks your getaway: it samples dozens of candidate spots in a radius around the blast, keeps only safe landings, and — by default — chooses somewhere no player has line of sight to (behind walls, around corners), falling back to the spot furthest from the nearest player. You teleport there silently — no sound, no particles — at full health, and undisguise in the same tick, so witnesses see a creeper vanish and a crater appear, never a player. Optionally snap your camera to face the explosion on landing, teleport the instant you ignite so you can watch the boom from safety (`tponexplode`), and prefer dry land over water (`avoidwater`).

**Decoy escape.** Take a hit that would drop you to/below a configurable health threshold while creeper-disguised and the hit is cancelled: you escape with the smart teleport at full health, and a *real* creeper spawns in your exact spot, takes the original hit — genuine hurt flash, knockback, and sound — then dies on the spot the same tick, leaving its gunpowder behind. Your attacker thinks they just killed a regular creeper. On the rare occasion no safe spot exists, you undisguise in place with the damage still cancelled and the decoy dies at your feet.

**Survivability suite.**
- **Silent regen** (off by default): heal half a heart every 5 seconds while creeper-disguised. No potion effect, no particles.
- **Death message suppression** (on by default): dying while creeper-disguised stays out of chat.
- **Arrow cleanup**: arrows that hit you are cleared a tick later so they don't float visibly on your invisible player model. Damage is unchanged.

**Movement speed matching.** While disguised as a mob, your walk speed matches that mob's normal speed. Configurable per mob.

**Barrier test markers.** Place barrier blocks to act as virtual observers when testing escapes — the escape picker treats them as players for line-of-sight and distance scoring. The marker material is changeable in the config.

## Requirements

- Paper or Purpur 1.21+ (built against 26.3)
- [LibsDisguises](https://www.spigotmc.org/resources/libs-disguises.81/)

## Installation

1. Drop `disguiseactions-1.3.0.jar` into your server's `plugins/` folder.
2. Make sure LibsDisguises is installed.
3. Restart the server.

## Usage

Disguise as a creeper with LibsDisguises (`/disguise creeper`), and your hotbar is stashed. Press **F** on slot 1 to undisguise, or **F** on slot 2 to go out with a bang.

## Commands

`/disguiseactions [setting] [value]` — view or change settings live, no restart needed. Changes save to `config.yml`. Tab-complete supported. Requires `disguiseactions.admin` (default: op).

| Setting | What it does | Default |
|---|---|---|
| `creeper` | Enable/disable the creeper action set | `true` |
| `fuse` | Fuse length in ticks (20 = 1 second) | `30` |
| `cooldown` | Seconds between ignites | `5` |
| `escape-radius` | How far the escape teleport may go (blocks) | `16` |
| `escape-samples` | Candidate spots sampled per escape | `48` |
| `escape-range-y` | Vertical scan range for landing spots | `8` |
| `face-explosion` | Snap camera to face the blast on landing | `false` |
| `tponexplode` | Teleport to safety the instant you ignite, so you can watch the explosion | `false` |
| `avoidwater` | Prefer dry land over water/lava for escape spots | `true` |
| `break-line-of-sight` | Escape somewhere no player can see | `true` |
| `los-range` | Range for line-of-sight checks | `48` |
| `decoy` | Enable/disable the decoy escape | `true` |
| `regen` | Enable/disable silent regen | `false` |
| `hints` | Show the hint sidebar while disguised | `true` |
| `speed` | Enable/disable movement speed matching | `true` |

`/disguiseactions reload` — reload the config from disk.

## Permissions

| Permission | What it does | Default |
|---|---|---|
| `disguiseactions.use` | Use the action triggers and speed matching | op |
| `disguiseactions.admin` | View/change settings with `/disguiseactions` | op |

## Configuration

Key sections in `plugins/DisguiseActions/config.yml` (all live-editable via `/disguiseactions`):

- `actions.creeper` — enable flag, fuse ticks, cooldown, hint sidebar (toggle, title, lines), escape tuning (radius, samples, range-y, line-of-sight, face-explosion, teleport-on-ignite, avoid-water).
- `survivability` — hide-death-message, silent regen (enable, amount, interval), decoy escape (enable, trigger health), barrier test markers (enable, material).
- `movement-speed` — enable flag plus per-mob speed overrides.

## Building from source

```bash
./build.sh
```

Requires JDK 25, `paper-api.jar`, and the LibsDisguises jar — see `build.sh` for the expected paths. No Maven needed.
