# SF2 LAN Duel Room — How to play with a friend (same WiFi)

Both PCs need: Shadow Fight 2 (Store) installed, Node.js, same SF2-LAN folder version
(mod `0.1.0`, proto `1` — shown at panel bottom; mismatch = `reject`).

## 0. Give your friend the same changes (one zip, no 578MB upload)
You already have it built: `SF2-LAN-friend-pack-v0.1.0.zip` (root folder,
~0.05MB, mod-only). Rebuild anytime: double-click
`mod\launcher\Export-FriendPack.cmd` (or `npm run export-pack`).
Send that ONE zip (Discord/USB). Friend: unzip anywhere →
`mod\launcher\Install.cmd` (copies THEIR Store game, applies overlay) →
`mod\launcher\Play.cmd`. Both panels must show the same `mod vX`.
Never copy your `game/` (578MB) or `profile/` — each PC builds its own.

## 1. Start (both PCs)
1. Double-click `mod\launcher\Play.cmd`.
2. Leave the `SF2 LAN relay` window open.
3. Game opens at `http://127.0.0.1:8788/index.html`. Click the `LAN` button top-right.
4. Play one solo fight once so your save (`SF2User`) exists, then press `Re-read from game`.
   You should see `YOU: NAME · lvl N` under section 2.

## 2. Connect
- **Host PC:** press `I am the host`, then `Show my IP` (or `Copy my IP`).
  Example: `192.168.0.116 (WiFi)`. Send this IP to your friend (chat, voice).
- **Friend PC:** type that IP into `friend's IP` field, press `Join`.
- Both should see status pill `online`, `connected to … as name (#id)`,
  and section 3 `Players in room` + `next duel / YOU vs FOE`.

If `Join` hangs on `connecting… / unreachable`:
- Same WiFi? No guest-network isolation / VPN.
- Host firewall: allow `node` on private network, or open TCP `8789` inbound.
  Quick test on friend PC browser: `http://<host-ip>:8789/lan` should say
  `SF2 LAN relay…` (426), not timeout.
- Both on same mod version (panel footer).

## 3. Duel (honor-system simultaneous fight + live input ghost)
1. Either side presses `Duel` next to the peer → friend gets `Accept?` confirm.
   Challenger sees `challenge sent - waiting…` (30s timeout, `Cancel` to abort).
   Friend sees `incoming challenge`, accepts → challenger sees
   `both ready - press Start round` (button enables only now).
2. Challenger presses `Start round` → 7s server-synced countdown on both screens.
3. Agree beforehand what to fight (e.g. same Tournament stage). Fight your local AI.
   During `GET READY / FIGHT` both PCs stream pressed fight keys at 10Hz
   (`duel.input` relay). Watch `live inputs:` in section 4:
   `you: W K · foe (38ms ago): A L · net 12ms`. This is a ghost HUD only
   by default — it does NOT move the foe fighter in your game yet.
   TEST option: tick `Mirror foe inputs` → foe keys are injected as synthetic
   key events into YOUR fight too (BOTH steer ONE fighter — proof the 10Hz
   link can drive the engine). Turn it OFF for real duels; it releases keys
   automatically when the round ends. True versus (foe drives P2/AI slot via
   the `Hc` hook) is still experimental — see REALTIME-FINDINGS.md.
4. Press `I won / I lost`. Both must report: match = point scored,
   mismatch (both win / both lose) = `disputed - replay!`, no point.
   No foe report in 15s = your claim scored as unconfirmed.
   `Rematch` re-challenges same foe (keeps 0-0 series), `Reset 0-0` clears.
5. Optional beta: `Put opponent in my game` shows a preview
   (`name · lvl · Weapon=…`), asks confirm, auto-backs up, copies foe
   name + level + gear (Weapon/Armor/Helm/Ranged/Magic) as an `IsFake` bot.
   Re-press replaces the old clone (never stacks). Restart `Play.cmd`
   to fight them, `Revert save` when done.

This is NOT live shared-screen versus — both fight locally at the same time
and report honestly. True live sync is Phase 3 research.

## Troubleshooting
- No `LAN` button: you opened Store version, not `Play.cmd` URL. Use `127.0.0.1:8788`.
- `fighter card: no save found`: play solo once, `Re-read`.
- `Version mismatch`: copy the whole `SF2-LAN` folder again so both match.
- Relay logs: host `SF2 LAN relay` window shows `joined / left`.
