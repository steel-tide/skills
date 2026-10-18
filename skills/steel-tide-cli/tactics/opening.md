# Opening: the first ten minutes

## Before the whistle

1. **Check the brain will take the seat.** `steel status` says `brain  takes the
   seat when the match starts` in the lobby. A doctrine chosen now is the one it
   opens with (`steel auto --doctrine armored`); leave it on `auto` when you do not
   yet know the map.
2. **Read the reach line** once the match is on. `steel brief` prints `reach  the
   enemy is across water` when no land route exists. Across water the war is
   ships, aircraft and lifts (see `naval.md`); on land it is tanks.
3. **Learn the ground.** `steel map` for the shape, `steel deposits` for the ore.
   The deposits between you and the enemy are the ones that will be fought over.

## Let the brain open

The brain's opening is the veteran's, and it is strong: most of the free ore
taken in the first two or three minutes, the first factory at about three,
extractor upgrades from the fourth, the War Lab and a doctrine by the fifth,
Deep-Core extractors from the eighth, an army from the seventh and waves after.
Do not fight it for the economy. Spend your attention on what it cannot see.

## What it cannot do: see

A Veteran sees the whole map, and every other AI and your brain see what their
side sees. Against a Veteran that is the gap, and these close it (against the
others they are simply good play):

- **Find the enemy by minute five.** The brain sends a scout to every start it has
  not seen, but a scout dies. `steel brief`'s `doing` line says `no enemy known
  yet: scouting` until it finds a building. If it still says so at 4:00, send a
  cheap unit yourself (`steel move <id> <their start>`), and give the brain a
  `--target` when you know where they are.
- **A radar early.** `steel auto --reserve 400`, then `steel build radar --near @hq`
  (and `steel auto --reserve 0` once it is laid) by minute five or six, on the
  side facing the enemy. It lifts the fog 24 tiles round it, through night and
  rain: every turret and gun inside it then fires at its full reach at things
  it could never see for itself, and the brain's memory fills with what it shows.
  The brain would build one near minute seven; earlier pays.
- **Artillery you cannot see.** A howitzer reaches 11 tiles and an MLRS 13 —
  past the sight of most of what they shoot at, and a Veteran's guns fire at
  whatever they reach, seen or not. Units and buildings dying with nothing in
  sight means artillery, and the loss line names the gun (`lost mbt x3 to mlrs
  x2`): look along the line to the enemy for open ground or water within 13
  tiles, get vision there (radar, a fast unit), and send tanks into it by hand.
  `--favour arty=2` gives you counter-battery, and a script can pull a group
  out from under the shells in the second they land (`scripts.md`).
- **Know what they build.** `steel enemies` lists what you have seen. An airbase
  or helicopters: `--favour flak=2,sam=2` and AA turrets. A radar plus a reactor:
  warheads are coming (`warheads.md`), so SAM sites now.

## Timings to check

| By    | Check                                                                                   |
| ----- | --------------------------------------------------------------------------------------- |
| 3:00  | four or more extractors, a factory going up; the scout on its way                       |
| 5:00  | the enemy's base found; the War Lab up and a doctrine chosen (`steel tech`)              |
| 6:00  | a radar standing on the enemy's side                                                    |
| 8:00  | an army at home worth the enemy's first wave; turrets on the approach it will come by   |
| 10:00 | Deep-Core extractors rising; you know their army's mix and have steered the answer      |

A check that fails is yours to fix by hand: a `build`, a `produce`, a `move`.

## Across water the clock is slower

When the enemy is across water, attacks arrive later and in smaller lifts, and the
brain plays the island plan (a navy yard and an airbase first, engineer boats for
the islets). Add coastal cannons and SAM sites early, because what comes is ships,
aircraft and warheads.
