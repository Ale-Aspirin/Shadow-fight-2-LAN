# Real-time versus — feasibility spike (Phase 3)

Date: 2026-10-01. Bundle: `game/sf2.786a1523.js` (Store 1.0.7.0, in-game v1.1.31).
Method: read-only scan of `game/index.html`, `windows-game-interface.js`,
`game/lan/*`, `mod/overlay/*`. No engine patches.

## TL;DR — GO with limits

- **Input injection: FEASIBLE.** All devices (`Mk` keyboard, `Nk` pointer,
  `Lk` mouse, `of` touch, `uf` gamepad) converge to `nc.wl(code)/release(code)`
  + `notify`. Synthetic `KeyboardEvent` on `document`, `PointerEvent` on canvas,
  virtual gamepad, or direct `state.wl()` all work without touching `sf2.*.js`.
- **Clock: FEASIBLE.** Single `rAF` owner (`Ug.start/stop`), fixed step
  `Jm=.0166` with accumulator (`Ys`), update (`aa/Kja`) / render (`Fa/Jja`)
  split. `J.I.Nc/vk/time` gives frame clock; our `lan-pre.js` `rAF` coexists.
- **Determinism: PLAUSIBLE.** Seeded LCG (`Vk/ay`, `1103515245`, mod 2^31) via
  `Da.vg.sf()/wT/vT`. `Math.random` not in sim. Must share
  `{J.seed, RandomGroupSeed, RandomRuleSeed}` and suppress time reseeds
  (`Da.AL()` on round init, `fd.getDate()` animation timers).
- **State snapshot: HARD.** No `HP/x/y/snapshot/serialize` fight API.
  Health in `Se` scene + `ERule*` objects, position in scene graph
  (`H` vectors, `Velocity/From/To`), damage via `Damage/Defense/Critical` tags.
  Requires reverse-engineering fighter/scene fields + DOM/canvas observers
  (`FightPause`, `Round_Done`, `label_win/lose`, `HealthBar_*`).
- **Netcode in engine: NONE.** `WebSocket/RTC/multiplayer/versus/remote/
  snapshot` = 0 hits (except `ERulePvp` stage rule, `FightPVP` enum stub,
  `ghost_kunai` item). Only netcode is our relay
  (`duel.req/ready/accept/result`, countdown, chat).

## Hooks found

- Boot: `index.html` → `GameInterface.init([... sf2.786a1523.js])` →
  `SF2.main (=J.rfb)`. Pre-boot `lan-pre.js` forces `standalone=true` (offline).
- Input IDs: `W,D,S,A,K,L,O,P` blocked from default (`q1().preventDefault`) =
  fight keys. Directions `1-8` with `jCa` invert, buttons `Punch/Kick/Ranged/Magic`
  (`ERuleNoButton`). `Nk.map: pointerId->slot` needs distinct remote pointerId.
- Player vs AI: `Tactic` → `Hc` object + `Jj(isAI)/Gu(controlled)` flags
  (`NotAI/Controlled` attrs). Replacing P2 `Hc` with remote input is the
  versus hook; `P.Hfa` tactics are deterministic if seeded.
- Fight end: read `FightPause` visibility, `label_win/lose`, `Round_Done`,
  `HealthBar_*`, or `ca.round/du` fields — not a clean event.

## Decision

- **Do NOT attempt full state-sync / rollback netcode now** (weeks, fragile).
- **DO attempt Phase 4a:** same-PC 2-input proof (local keyboard + virtual
  gamepad/`wl()` driving P2, shared seed, honor countdown already synced).
- **Then Phase 4b:** remote input forwarding at 10Hz with delay-based lockstep;
  abort to honor+bot if desync >500ms or no stable `Hc` hook.

## Next (Phase 4a scope)

1. Add `window.__SF2LAN.debug` read-only introspection (`clock/seed/inputs`)
   + synthetic-input test buttons (keyboard/pointer/gamepad paths).
2. Manual test: spawn training fight, drive P2 via second channel, confirm no
   interference with P1.
3. Measure: input→effect latency, countdown skew via existing server-offset,
   `Da.AL()` reseed frequency. Go/no-go for 4b on those numbers.
