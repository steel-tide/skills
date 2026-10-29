# Opening: the first twelve minutes

## Before the whistle

1. **Read the ground.** `steel deposits` for the ore (the near ones are
   `free`; the ones between you and the enemy will be fought over), `steel
   map` for the shape. `steel brief` prints `reach  the enemy is across
   water` when no land route exists: then the war is ships, aircraft and
   lifts (see `naval.md`); on land it is tanks.
2. **Know where the enemy starts.** On most maps the starts are at opposite
   corners or sides; the far deposits in `steel deposits` point the way.

## The build order

The seat is yours from the first second, and the economy decides the match
by minute ten. Do this in the first turn, then keep every engineer busy:

```bash
steel build extractor --deposit <nearest> --by 3        # both engineers on ore at once,
steel build extractor --deposit <next> --by 2           # the rest queued behind (--queue)
steel build extractor --deposit <third> --by 3 --queue
steel build extractor --deposit <fourth> --by 2 --queue
steel build extractor --deposit <fifth> --by 3 --queue
steel produce hq engineer -n 2                          # four engineers by 1:00
steel tactic guard                                      # the standing rules, before the first unit
steel tactic retreat --all --below 35
```

Then, as metal allows (the refusal gives the figures; a building starts at
three quarters of its price):

| By    | Lay                                                                                                   |
| ----- | ----------------------------------------------------------------------------------------------------- |
| 1:30  | a power plant (`power 9/20` in the economy line means the base is starved: everything slows)          |
| 2:30  | a factory; `steel rally factory <x,y> --join army` beside it, and `steel produce factory ltank -n 2 --repeat` |
| 3:30  | a second power plant; `steel produce reserve 500` so the standing tanks leave money for buildings     |
| 4:30  | the War Lab (`warlab`), then `steel research <doctrine>` at once: hammer, anvil or lightning           |
| 5:30  | a cannon turret on the approach the enemy will come by; the factory upgraded (`steel upgrade factory`)  |
| 6:30  | a radar on the enemy's side (`radar`): it lifts the fog 24 tiles round it, and every gun inside fires at full reach |
| 7:00  | extractor upgrades, one at a time (`steel upgrade <id>`: an Advanced extractor mines twice a plain one); the standing order switched to `mbt -n 2` once the factory is upgraded, with `flak -n 1` beside it |
| 9:00  | a second factory; the first outlying deposit claimed, with a turret on it                             |
| 12:00 | twelve to sixteen units in `army`, a mix (tanks, a tank destroyer or two, flak, two howitzers), and `steel enemies` read |

A check that fails is yours to fix now; nothing else will. Every turn: are
the engineers idle (`crews` in the brief)? Is metal piling up (over 600 with
nothing committed: spend it)? Is a queue empty (`steel produce`)?

## What the first ten minutes are for

- **Find the enemy without losing a unit.** A light tank sent into their base
  dies to the HQ's gun; a scout is a unit that stops short. Send one to the
  middle of the map (`steel move <id> <x,y>`) and read `steel enemies` when it
  spots something; the radar does the rest.
- **Lose nothing early.** Every unit alive at minute twelve is worth two
  built then. The guard and the retreat rule keep the first tanks alive; an
  outlying extractor without a turret is a unit-worth of metal given away.
- **Artillery you cannot see.** A howitzer reaches 11 tiles and an MLRS 13,
  past the sight of most of what they shoot at, and a Veteran's guns fire at
  whatever they reach, seen or not. Units and buildings dying with nothing in
  sight means artillery, and the loss line names the gun (`lost mbt x3 to mlrs
  x2 — out of sight: pull back past 12 tiles`): pull back that far, get vision
  (the radar, a fast unit), then `steel priority <id>` on it and tanks at it
  by hand. `steel tactic kite` on your own batteries keeps them out of theirs.
- **Know what they build.** `steel enemies` lists what you have seen. An
  airbase or helicopters: flak and SAM launchers on the standing orders, AA
  turrets. A radar plus a reactor: warheads are coming (`warheads.md`), so SAM
  sites now. Heavy tanks: tank destroyers and howitzers, not light tanks.

## If the base falls behind

Against a Veteran the economy race is lost by minute eight if the table above
slips. `steel auto --run economy,research` hands the base and the tech ladder
to the brain — the veteran's own opening, under your fog — and keeps
production and the army yours. Call it early rather than late; `steel direct`
takes the base back when you want it.

## Across water the clock is slower

When the enemy is across water, attacks arrive later and in smaller lifts. A
navy yard and an airbase come before the second factory, engineer boats claim
the islets, and coastal cannons and SAM sites go up early, because what comes
is ships, aircraft and warheads.
