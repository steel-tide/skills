---
name: steel-tide-cli
description: >-
  Play Steel Tide, a real-time strategy game, through the `steel` command line —
  as the commander of one faction on a dedicated server. Use this whenever asked
  to play, join, or command a Steel Tide match, or to run the `steel` CLI. The
  game's own AI brain plays your seat second by second under your fog; you
  command above it — steer it, take any unit, building or decision into your
  own hands, set standing tactics, or turn it off and play by hand and by
  scripts of your own. Covers connecting, reading the battlefield, steering the
  brain, orders by hand, groups, routes, tactics, direct control and scripts.
license: MIT
---

# Playing Steel Tide from the command line

Steel Tide is a real-time strategy game about large unit battles. `steel` is its
command line: you join a server and command one faction, as a player at a
screen would.

The match does not pause for you, so you are never the only one playing your
seat. Three things do:

- **The brain** plays every second: the game's own AI, playing your seat with
  everything it sees. It opens the economy, techs, builds and sends waves by
  itself from the moment the match starts.
- **You** command above it. You steer it (`steel auto`) and give any order by
  hand when you judge better. A unit you order is yours until you hand it back
  (`steel attach`); a building you lay, a unit you queue or a research you start
  is counted by the brain as its own.
- **Standing tactics and scripts** act between your turns: rules you set once
  (`steel tactic`: guard the base, kite guns, retreat when hurt, focus fire) or
  code you write (`steel script`), applied to what your seat sees many times a
  second.

The enemy AI comes in four strengths. Relaxed, Standard and Seasoned play under
the fog of war as you do. The **Veteran** sees the whole map and shoots at what
it cannot see, so the brain alone loses to it more often than it wins; what wins
is you. Your edge is judgement the brain does not have: finding out what the
enemy is doing and answering it — scouting, a radar, the right counters, where
and when to strike, SAM sites before the warheads come, staying out of the
reach of artillery you cannot see. Look, decide, steer, and step in where it
matters.

This file is the verbs and the loop. How to win is in `tactics/`, one chapter per
part of the game. Read `tactics/opening.md` before every match, then load the
chapter that matches what is happening; do not load them all.

| When                                                         | Load                   |
| ------------------------------------------------------------ | ---------------------- |
| a match is starting: the brain, the map, your first calls    | `tactics/opening.md`   |
| income, expansions, upgrades, the War Lab                    | `tactics/economy.md`   |
| the enemy fields something new: what beats it                | `tactics/counters.md`  |
| the base is threatened or under attack (`!` lines, `steel threats`) | `tactics/defense.md`   |
| planning or running an offensive                             | `tactics/attack.md`    |
| the enemy is across water (`reach` says so)                  | `tactics/naval.md`     |
| a radar, reactor or silo on either side                      | `tactics/warheads.md`  |
| losing: army gone, HQ gone, a warhead landed                 | `tactics/recovery.md`  |
| a rule no tactic covers must hold between your turns: a script | `tactics/scripts.md` |

## Setup

`steel` is installed separately (`curl -fsSL https://steelti.de/cli.sh | sh`).
Confirm it is on PATH with `steel --version`.

Join a server. A private room needs its address and join code (ask the user
when you do not have them); the game's public rooms need no code and are
joined by name:

```bash
steel servers                                        # the public rooms: seats, status, location, ping
steel connect <server> --name <a name>               # a public room by its name
steel connect <address> --code <join code> --name <a name>
steel config server home <address> --code <join code> --as <a name>   # save a room once …
steel connect home                                                     # … and join it by name
```

Choose a `recruiting` or `free` room, or a `lobby` with company. A `playing`
room seats you in the stands until its match ends; `another version` and
`unreachable` rooms cannot be joined.

Then `steel status` until `phase` reads `playing`. If `seat` reads `spectator`,
a match was already on: you are watching it and will be seated when the lobby
returns (`phase` reads `lobby`; then `steel ready`).

**Running the room.** The host connects with `--host-key <key>`; when no host is
connected, the seated player longest in the room is the acting host (`steel
status` says `(acting host)`), and `steel lobby` marks them. Whoever runs the
room sets it up and starts it:

```bash
steel lobby                                   # seats, map, mode, time limit, factions on offer
steel lobby slot 1 ai:veteran                 # a seat: human, ai[:relaxed|standard|seasoned|veteran] or closed
steel lobby set --map caldera --time-limit 45 # the match; steel help lobby has every setting
steel lobby recruit                           # list the room for strangers (five minutes, renewable)
steel start
```

`steel lobby slot me --faction <id>` takes a faction where the room offers
several. `steel spectate --player <name>` moves a player to the stands (`--off`
back), and `steel lobby host <name>` hands the room to a seated player. If a
connect fails over a mod or a world the room requires, the host has to update
the server; nothing on your side fixes it.

The brain can be steered before the whistle (`steel auto --doctrine armored` in
the lobby) and opens that way.

## The command loop

1. **Look.** `steel brief` is the whole picture on one screen: economy, base,
   army, the enemy you know of, the threats in sight, deposits, recent events,
   and what the brain is doing (`brain` and `doing`: its doctrine, its
   offensive or next push, its objective, how many enemy buildings it knows).
   A loss names what killed it (`lost mbt x3 at 40,30 to mlrs x2`), even when
   the killer is out of sight. Drill in with `steel threats`, `steel enemies`,
   `steel deposits`, `steel map`, `steel tech`, `steel defs <unit>`.
2. **Decide.** Is the brain doing what the situation needs? Most of the time it
   is. When it is not — it cannot see the artillery shelling the base, it is
   building tanks against an air force, its offensive is going at the strong end
   of the enemy's base — steer it, or do the thing yourself.
3. **Steer or act.** `steel auto` changes what the brain does from its next
   second; a hand order does one thing now; a tactic keeps a rule.
4. **Wait.** `steel wait 45` lets the match run (game seconds) and returns the
   moment something that cannot wait happens: the base under fire, a loss, a
   warhead, a faction out. Woken so, it ends on the **threats** as they stand —
   who is coming, from where, how fast, what they have under their guns, what
   of yours answers — so you can act without another look. Its last line is
   your economy now. A fight it has already shown you does not wake it again
   at every shot; a new place or a building lost does. `--for produced,built`
   wakes on kinds you name instead; `--for none` is a plain timer.
5. Repeat, adapting as the enemy reveals itself.

**You are never the last to know.** Every reply — an order, a list, even a
refusal — opens with `!` lines for what happened since you last looked
(`brief`, `wait`, `events` or `threats`): read them before the rest, and run
`steel threats` when they name an attack.

## Steering the brain: `steel auto`

Every flag changes one thing and keeps the rest. `steel auto` alone shows what
the brain runs, what it is doing, and how many units you hold by hand.

- `--run economy,research,production,army,nukes` — the departments it runs (or
  `all`). A department left out is yours: the brain neither spends on it nor
  saves for it. Leave out `army` to fight every battle yourself; leave out
  `economy` to lay every building yourself.
- `--doctrine combined|armored|airpower|naval|turtle|blitz|auto` — the
  personality it fights by: which lines it feeds, how many turrets, how early it
  attacks, how it techs. `auto` lets it choose.
- `--favour mbt=2,flak=0,sam=3` — a weight on what it builds, times its own: 2 is
  twice as often, 0 never, 1 back to normal; `clear` drops them all. A unit
  favoured above 1 is always built at least a little, even before the brain has
  seen what calls for it. This is how you answer what you have seen;
  `counters.md` says what beats what.
- `--posture push|hold|auto` — `push` sends whatever stands ready at its
  objective now and keeps sending; `hold` never marches (it still defends);
  `auto` is its own clock.
- `--target x,y` — what its offensive marches on, in place of the nearest enemy
  building it knows (`none` to clear). Point it at the enemy's weak side, their
  outlying extractors, the silo.
- `--reserve 400` — metal it leaves on the balance for your own orders. The brain
  spends as it earns, so a radar or a SAM site you lay by hand waits for metal
  it happens to leave lying; set a reserve first, lay it, then set it back to 0.
- `steel auto off` takes the whole seat: every order is yours. `steel auto on`
  hands it back.

## Orders by hand

Anything the brain would do, you can do yourself, at any moment. Units are ids
(`12,15`), a group's name (`raid`), or predicates (`--idle --def buggy --near
40,30:8`, `--ground|--air|--sea`, `--armed`, `--engineers`, `--held` for the
units in your hands); places are `x,y` in tiles, `@<id>` or `@hq`. `steel help
<verb>` has every option.

- **Units:** `steel move <units> <x,y> [--attack]`, `steel attack <units>
  <target>`, `hold`, `stop`, `route` (a standing patrol), `board`, `pickup`,
  `unload`, `repair`, `assist`, `ability`, `hold-fire`. The unit is then yours
  until `steel attach <units>` (or `--all`) gives it back; `steel detach
  <units>` takes units without an order; `steel brief` counts what you hold
  (`hands`).
- **Groups:** `steel group raid 104,105,107` names a group (or by predicates:
  `steel group raid --def drake --near 70,10:12`). Then `move raid 76,3`,
  `attack raid 40`, `units raid`, `tactic focus raid`, `--group raid` anywhere,
  and `s.group('raid')` in a script. The dead fall out by themselves. Never
  carry ids in a script.
- **Routes:** `steel path <from> <to> [--air|--sea]` finds a way round every
  enemy gun you know of and says what it keeps clear of and what it has to pass
  under. `steel move <units> <x,y> --safe` sends them along it. This is how a
  raid reaches the extractors behind a turret wall, and how a lift skirts the
  SAM sites.
- **Tactics:** rules kept for you, many times a second, so the micro you would
  otherwise script is a verb:
  - `steel tactic guard` — what comes within 24 tiles of the base is met by
    the armed units in your hands, who go back where they stood once it is
    gone. **Set it at the whistle.** `--at x,y --radius N` posts it elsewhere.
  - `steel tactic kite --def arty,mlrs` — a unit a gun can reach steps out of
    the gun's reach, and goes back to the brain once clear. Artillery, rocket
    launchers, aircraft under SAM.
  - `steel tactic retreat --all --below 30` — a unit under 30% goes home and is
    let go once mended (`--at x,y` for another place).
  - `steel tactic focus raid` — the guns go on the weakest thing in reach.

  A tactic's orders are hand orders. On `--all` a tactic takes only the units
  already in your hands (the brain's own already defend and kite); `--def`, a
  group or ids take those units from the brain. `steel tactic` shows what each
  holds and has sent; `steel tactic stop <id>|--all` ends one and gives the
  units back.
- **Hold fire:** `steel hold-fire <units>` stops their automatic fire and
  pursuit while they keep moving and working: slip past a turret line, set an
  ambush, keep a scout unseen. `--off`, or any attack order, releases it;
  `steel show` reads `holding fire`.
- **Priority targets:** `steel priority <id>` marks a visible enemy, and every
  gun of yours that can shoot it from where it stands, the brain's included,
  picks it first. Nothing leaves the brain's hands: it is how you point the
  brain's guns at the artillery, the radar or the silo. Three marks at a time;
  `steel priority` lists them; `steel priority <id> --off` removes one.
- **Buildings:** `steel build <building> --near <x,y>` (a radar, SAM sites, a
  turret where the brain did not put one), `steel upgrade <ids>`, `steel sell
  <ids>`. The brain crews your blueprints and counts them toward its own plans.
- **Production and research:** `steel produce <factory> <unit> [-n N]`,
  `steel research <node>`. They go in the queue beside the brain's.
- **Warheads:** `steel nuke targets` (the best points to aim at, with what each
  destroys and the chance it arrives), `steel nuke launch <silo> x,y`. The brain
  launches too while it runs `nukes`; leave that out of `--run` to choose every
  target yourself.
- **Unit actions a mod adds:** `steel show <id>` lists them; `steel
  content-action <units> <module> <action> [--choice id] [--target id] [--at
  x,y]` uses one.

## Direct control and scripts

`steel direct` turns the brain off: nothing plays the seat but your orders,
your tactics and your scripts. `steel auto` hands it back.

A **script** is a file of TypeScript or JavaScript run against what your seat
sees every `every` seconds (0.2 at the least), whose orders go out as yours. It
is for a rule no tactic covers: a line kept busy, a department run your way, a
raid with a plan of its own.

```ts
export default {
  every: 0.5,
  tick(s) { /* the whole seat: s.units(), s.enemies(), s.group('raid'), s.events, s.memory ... */ },
  rules: {
    arty(u, s) {  // once a run for each of your units of this kind; also 'army' and '*'
      const close = s.enemies({ near: u, within: 6, armed: true, seen: 0 });
      if (close.length) s.move(u, s.away(u, s.nearest(u, close)!, 5));
    },
  },
};
```

- `steel script types > steel.d.ts` prints the whole API, for your editor.
- `steel script try <file>` runs it once now and prints the orders it would
  give, sending nothing. `steel script run <file>` runs it (again under the
  same name replaces it); `steel script` shows every script's runs, orders,
  refusals and errors; `steel script log [name]` what it said and why an order
  was refused; `steel script stop <name>|--all`.
- `steel lab --vs veteran --script <file>` (add `--direct` for a whole seat)
  plays it against the built-in AI, many seeds in a minute: prove a script
  there before a live match.
- A unit a script orders is yours until `s.attach` gives it back, as with any
  hand order. `s.steer({ run: [...] })` hands the brain only the departments
  you name: take `army` and play it by script.
- A script sees what your seat sees and nothing more, and reaches nothing but
  the match. `tactics/scripts.md` has the patterns and the limits.

## When something does not happen

A refused order says so plainly: `refused, nothing was done: <order>: <why>`,
with the figures (`not enough metal: 135 of its 180 is needed to start, 96 is
free`). Nothing was queued or placed.

Where a building may stand is the refusal most worth knowing:

- **The build area.** A side builds on the ground its headquarters hold; only an
  extractor or an offshore rig stands outside it.
- **Production slots.** Each headquarters opens a few, and every factory, yard
  and airbase takes one. With the HQ gone, none can be placed.
- **Frontline.** In that mode the map is sectors, and everything goes up only on
  sectors your side holds and supplies. A sector is taken by armed ground units
  or ships standing at its point, next to ground you already supply; the
  victory points drain the reserve of whoever holds fewer. The brain sends
  squads to the points by itself; a build by hand goes near what you already
  hold.

If the brain seems stuck, look at `steel auto` and `steel brief` first: `no enemy
known yet: scouting` means it has found nothing to march on (scout for it, or
give it a `--target`); `gathering … next push from 12:30` means it is waiting to
march in a body (`--posture push` sends it now); a department you left out of
`--run` is waiting on you.

If `steel status` says the room's server has no brain for your seat, `steel`
plays it with an older autopilot of its own: `steel help keep`, `mix` and
`force` are its verbs, and `steel brief` shows its plan.

## Rules

- Use only `steel` verbs, and the script files you write for `steel script`.
  Do not edit the game's files, restart the server, or read the server's files.
  You see only what a player sees: the fog of war is real.
- Every output is plain text; add `--json` to any command for structured data.
  Exit 0 means done, 1 refused (the message says why), 2 usage, 3 no session or
  the server is gone.
- One word per argument: an argument with spaces in it reached `steel` whole
  (zsh does not split an unquoted `$var`; use `${=var}`, an array, or separate
  words).
- Keep going until `steel status` shows phase `ended` or you are eliminated. Then
  `steel disconnect` and report the result, how the match turned, and what you
  would change.
- Under a time limit, the faction still standing with the most unspent metal
  wins at the deadline, and equal balances across teams are a draw. In the last
  minutes bank instead of spending: `steel auto --reserve 10000` (its most)
  keeps the brain's hands off the balance.

## Quick reference

```bash
steel servers                                         # the public rooms
steel connect <address> --code <code> --name <name>   # join; --host-key to host; a public room by its name
steel status                                          # link, phase, seat, economy, the brain
steel brief                                           # the whole situation, and what the brain is doing
steel threats                                         # every enemy group in sight: worth, distance, heading, what it hits, what of yours answers
steel enemies | steel deposits | steel map | steel tech   # drill in
steel defs <unit>                                     # a unit's stats and counters
steel auto [on|off] [--run depts] [--doctrine D] [--favour u=w,...] [--posture push|hold|auto] [--target x,y|none] [--reserve M]
steel move <units> <x,y> --attack | steel attack <units> <target>   # take units into your hands
steel group <name> <units> | move <name> <x,y>        # name a group; order it by name
steel tactic guard | kite <units> | retreat <units> --below 30 | focus <units>   # standing rules; tactic stop <id>|--all
steel path <from> <to> [--air] | move <units> <x,y> --safe   # a route round known guns, and units sent along it
steel hold-fire <units> [--off]                       # no automatic fire until released
steel priority [<id> [--off]]                         # mark an enemy for every gun of yours; nothing leaves the brain
steel attach <units>|--all | steel detach <units>     # give them back; take them without an order
steel direct                                          # the brain off: your orders, tactics and scripts alone
steel script run <file> | try <file> | stop <name> | log [name] | types   # your own code on the seat
steel build <building> --near <x,y> | steel produce <factory> <unit> | steel research <node>
steel nuke targets | steel nuke launch <silo> x,y     # warheads
steel wait <seconds> [--for kinds|none]               # let it run; wakes on what cannot wait, ends on the threats
steel disconnect                                      # leave
steel lab --vs veteran [--script <file>] [--direct]   # your seat (and scripts) against the built-in AI, headless
```
