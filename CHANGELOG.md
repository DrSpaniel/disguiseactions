# Changelog

## 1.3.0
- Added `tponexplode` setting: teleport to safety the instant ignite is triggered, so you can watch the explosion from a safe spot instead of blinking away at the last tick. Off by default. (`/disguiseactions tponexplode true`)
- Added `avoidwater` setting: the escape spot picker now prefers dry land over water and lava, falling back to wet spots rather than failing if no dry land is in range. On by default. (`/disguiseactions avoidwater true`)
- Both are live `/disguiseactions` settings with tab-complete, saved to config.yml.

## 1.2.5
- Removed the action books entirely. Disguising now stashes and clears the hotbar with nothing placed in it.
- Triggers are F (swap hands) on the hotbar slot: F on slot 1 = undisguise ("poof"), F on slot 2 = ignite. The swap is consumed so the hotbar stays locked.

## 1.2.4
- Books returned as visual labels in slots 1-2 ("Poof", "Ignite Fuse"), but the trigger moved from right-click to the F key.

## 1.2.3
- Returned to book triggers: PDC-tagged "Poof" (slot 1) and "Ignite Fuse" (slot 2) books placed on disguise, right-click to activate. Books can't be dropped, moved, or lost on death; the real hotbar restores exactly on undisguise.

## 1.2.2
- HotbarManager restored (bookless): the full 9-slot hotbar is stashed and cleared on disguise, locked against clicks, drags, shift-clicks, number-key swaps, F-swaps, and pickups while disguised, and restored exactly on undisguise, logout, or death (including into death drops).

## 1.2.1
- Removed book triggers in favor of empty-hand right-click air on slots 1-2. (Later discovered the vanilla client sends no packet for empty-hand air right-clicks, making this undetectable server-side — reverted in 1.2.3.)
- Decoy escape redesign: a successful escape now restores full health; the decoy creeper takes the original hit and dies on the spot the same tick, leaving its gunpowder drops behind.
- Stuck arrows are cleared a tick after hitting a disguised player (damage unchanged) so they don't float on the invisible player model.
- Added the per-player hint sidebar, shown only while disguised; the previous scoreboard is restored exactly on undisguise.

## 1.2.0
- Initial creeper action set: "Ignite Fuse" and "Poof" books in slots 1-2, right-click to trigger.
- Hotbar stashed on disguise, restored on undisguise/logout/death.
- Creeper bomb: invisible, AI-less, invulnerable fuse creeper with real hiss and explosion; smart escape teleport at the last tick.
- Decoy escape (toggleable, default on), silent regen (toggleable, default off), death message suppression (default on), barrier-block test markers, movement speed matching.
