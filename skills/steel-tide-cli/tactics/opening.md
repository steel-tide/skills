# Opening: the first ten minutes

## Before the first order

1. **Clear what the last match left.** The plan, the mixes and the forces live in
   the session, not the match, so a new match starts with the old goals. First
   commands: `steel plan reset`, then `steel force <name> disband` for each force
   `steel forces` still lists.
2. **Read the reach line.** `steel brief` prints `reach  the enemy is across
   water` when no land route exists. Across water the war is ships, aircraft and
   lifts (see `naval.md`); on land it is tanks.
3. **Learn the ground.** `steel map` for the shape, `steel deposits` for the ore
   and its `reach`. The deposits between you and the enemy are the ones that will
   be fought over; the ones behind your base are safe income.

## Army before greed on land

The first real attack on a land map tends to come at 10 to 12 minutes, as a
body of 8 to 10 units, and in a match with several enemies it can come from two
sides at once. An opening that spends everything on extractors, upgrades and
engineers meets it with a few hundred metal of army and loses the base.

Aim for these by the clock:

| By    | Have                                                                                 |
| ----- | ------------------------------------------------------------------------------------ |
| 3:00  | 4 extractors, a factory, 2 MG turrets toward the enemy                                |
| 6:30  | the factory at level 2 (`keep factory2 1 --save`)                                     |
| 8:00  | about 2,000 metal of army at home (battle tanks, a tank destroyer or two, 2 flak), cannon turrets at the front |
| 10:00 | a force posted where the first attack will land; only now the extractor upgrades      |

A build order that does this:

```bash
steel plan reset
steel expand <near deposit>          # one per safe deposit, by id
steel keep factory 1
steel keep mgturret 2 --front
steel keep factory2 1 --save
steel keep cannonturret 2 --front --then
steel mix factory mbt=3 td=1 flak=0.5:3 arty=1:6
steel force main --size 10 defend <front x,y> --radius 15
steel plan set engineers=4
```

- **Three or four engineers.** Each one is 200 metal of no army.
- **Upgrade extractors once the base is safe**, not before (see `economy.md`).

## Across water the clock is slower

When the enemy is across water, attacks arrive later and in smaller lifts, so an
economy opening pays: ten extractors and +1,500 metal a minute by minute 9 is
reachable, with engineer boats claiming the islets. The autopilot's island plan
(naval yard and airbase first) is a sound start; add coastal cannons and SAM
sites early, because what comes is ships, aircraft and warheads.

## First scouting

`steel scout on` from the start; it sends a cheap drone or buggy. By minute 6 to
8 you want to know where the enemy base is and what it builds. Read `steel
enemies` and turn it into a `mix` (see `counters.md`). A radar, reactor or silo
in that list means warheads are coming: start `warheads.md` now.
