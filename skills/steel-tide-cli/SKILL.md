---
name: steel-tide-cli
description: >-
  Play Steel Tide, a real-time strategy game, through the `steel` command line —
  as the commander of one faction on a dedicated server. Use this whenever asked
  to play, join, or command a Steel Tide match, or to run the `steel` CLI. You
  hold the seat: every building, unit and attack is your call, and the session
  keeps your standing orders between turns — tactics for your units (guard,
  kite, retreat, focus, assault as one body), production kept running, new
  units gathered into groups, scripts of your own. The game's own AI brain can
  be handed any department or the whole seat on command. Covers connecting,
  reading the battlefield, orders, groups, routes, tactics, standing
  production, the brain and scripts.
license: MIT
---

# Playing Steel Tide from the command line

Steel Tide is a real-time strategy game about large unit battles. `steel` is its
command line: you join a server and command one faction, as a player at a
screen would.

The seat is yours. Nothing builds, produces or fights unless you order it. The
match does not pause for you, and a turn of yours takes tens of seconds of
match time while a fight turns in one, so the session keeps **standing orders**
for you between turns:

- **Tactics** (`steel tactic`): the micro a player does with their fingers.
  A guard that meets what comes at the base and returns. Hurt units sent home
  and let go once mended. Artillery kept out of the guns that can reach it.
  Guns put on the weakest thing in reach. An **assault**: a group marched to a
  place as one body, fighting what it meets together, holding the place, and
  falling back by itself when it has lost half its worth.
- **Standing production** (`steel produce … --repeat`): a unit kept on a
  factory's queue for the rest of the match, refilled as each comes out,
  never spending under the reserve you set for buildings.
- **Rally into a group** (`steel rally … --join army`): every unit a factory
  turns out joins the group, so the tactics on the group take it up at once.
- **Scripts** (`steel script`): your own code, run against what your seat sees
  many times a second, for a rule no tactic covers.

The **brain** is the game's own AI, the one the built-in factions play with. It
is off until you call it. `steel auto on` hands it the whole seat; `steel auto
--run economy,research` hands it the base and keeps production and the army
yours. It plays under your fog as the Seasoned persona, and loses to a Veteran
more often than it wins. What wins is your judgement: finding out what the enemy
fields and answering it, a radar early, the right counters, an army that is a
mix and moves as a body, SAM sites before the warheads come, and staying out of
the reach of guns you cannot see.

This file is the verbs and the loop. How to win is in `tactics/`, one chapter per
part of the game. Read `tactics/opening.md` before every match, then load the
chapter that matches what is happening; do not load them all.

| When                                                         | Load                   |
| ------------------------------------------------------------ | ---------------------- |
| a match is starting: the build order, the first calls        | `tactics/opening.md`   |
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

## The command loop

1. **Look.** `steel brief` is the whole picture on one screen: economy, base,
   army, the enemy you know of, the threats in sight, deposits, recent events,
   and the tactics that run. A loss names what killed it (`lost mbt x3 at
   40,30 to mlrs x2`), even when the killer is out of sight. Drill in with
   `steel threats`, `steel enemies`, `steel deposits`, `steel map`, `steel
   tech`, `steel defs <unit>`, `steel produce` (the standing orders), `steel
   tactic` (what each rule holds and did last).
2. **Decide.** What does the next minute need: a building, a counter in
   production, the army moved, an attack called off? Idle engineers, metal
   piling up, a factory with an empty queue and an enemy unit you have no
   answer to are the four things to check every turn.
3. **Order.** A hand order does one thing now (`build`, `produce`, `move`,
   `attack`, `research`, `upgrade`). A standing order keeps doing it:
   `produce --repeat`, `rally --join`, `tactic`. Prefer standing orders: a
   decision that holds between your turns is worth ten that do not.
4. **Wait.** `steel wait 45` lets the match run (game seconds) and returns the
   moment something that cannot wait happens: the base under fire, a loss, a
   warhead, an assault that reached its goal or turned for home, a faction
   out. Woken so, it ends on the **threats** as they stand — who is coming,
   from where, how fast, what they have under their guns, what of yours
   answers — so you can act without another look. Its last line is your
   economy now. A fight it has already shown you does not wake it again at
   every shot; a new place or a building lost does. `--for produced,built`
   wakes on kinds you name instead; `--for none` is a plain timer.
5. Repeat, adapting as the enemy reveals itself.

**You are never the last to know.** Every reply — an order, a list, even a
refusal — opens with `!` lines for what happened since you last looked
(`brief`, `wait`, `events` or `threats`): read them before the rest, and run
`steel threats` when they name an attack.

**Keep the loop short.** Several orders a turn, then one wait of 30 to 60
seconds. Never pause the match to think, and never run `steel lab` during a
live match: it is a tool for proving a script between matches.

## Standing orders

### Tactics: `steel tactic`

A rule the session applies four times a second to the units you name — ids,
a group, `--def mbt`, `--all`. `steel tactic` lists them with what each holds,
has sent and did last; `steel tactic stop <id>|--all` ends one and leaves the
units where they stand.

- `steel tactic guard` — what comes within 24 tiles of the base is met by
  every armed unit in your hands, who go back where they stood once it is
  gone. **Set it at the whistle.** `--at x,y --radius N` posts it elsewhere.
- `steel tactic retreat --all --below 35` — a unit under 35% goes home and is
  let go once mended (`--at x,y` for another place). Set it at the whistle
  too: a unit repaired fights again, a unit dead does not.
- `steel tactic kite --def arty,mlrs` — a unit a gun can reach steps out of
  the gun's reach and goes back to what it did once clear. Artillery, rocket
  launchers, aircraft under SAM.
- `steel tactic focus army` — the guns go on the weakest thing in reach.
- `steel tactic assault army --to 60,50` — **the attack.** The group marches
  there as one body on a route round the turrets you know of; a unit that
  runs more than 6 tiles ahead waits for the rest (`--spread`); what the body
  meets it closes on together; at the place it holds like a guard until you
  stop it or send a new assault; and when it has lost half the most it was
  worth (`--below 50`) it falls back to where it set out and tells you
  (`assault3: beaten at 29,36: 6 units worth 720 left of 1680; falling back to
  19,17`). Setting out, reaching and falling back are `contact` lines, which
  wake `steel wait`.

Rules on one unit run in an order — retreat, kite, assault, guard; focus only
picks targets — so `tactic retreat army`, `tactic kite army --def arty` and
`tactic assault army --to x,y` on the same group are one attack: the hurt go
home, the batteries keep their distance, the body marches.

### Production that keeps going

```bash
steel produce factory mbt -n 2 --repeat     # two mbt on this factory's queue at all times, for the rest of the match
steel produce factory flak -n 1 --repeat    # and a flak beside them
steel produce reserve 500                   # refills never spend the last 500: that is for your buildings and research
steel produce                               # the standing orders, and what holds each back (metal, a queue full)
steel produce stop [factory]                # end them (all, or one factory's)
```

A factory with an empty queue is the most common way an agent loses: it looks
every thirty seconds and the queue ran dry in ten. Set the standing orders at
the whistle and change them when `steel enemies` changes (`counters.md`). When
a building you want is refused for metal, raise the reserve until it is laid,
then lower it.

### Groups, rally, routes

- `steel rally factory 22,16 --join army` — the factory's units gather at
  22,16 and join the group `army` as they come out. With `steel tactic guard
  army`, `tactic retreat army` and the rest already set on the group, every
  new unit is covered the moment it appears. `--join none` stops it.
- `steel group raid 104,105,107` names a group (or by predicates: `steel group
  raid --def drake --near 70,10:12`; `--add`, `--remove`). Then `move raid
  76,3`, `attack raid 40`, `units raid`, `tactic focus raid`, `--group raid`
  anywhere, and `s.group('raid')` in a script. The dead fall out by
  themselves. Never carry ids in a script.
- A bare word that names no group but a kind selects every unit or building
  of that kind: `move ltank 40,20`, `rally factory 22,16`, `upgrade extractor`.
- `steel path <from> <to> [--air|--sea]` finds a way round every enemy gun you
  know of and says what it keeps clear of and what it has to pass under. `steel
  move <units> <x,y> --safe` sends them along it. This is how a raid reaches
  the extractors behind a turret wall, and how a lift skirts the SAM sites.

## Orders by hand

Units are ids (`12,15`), a group's name (`raid`), a kind (`mbt`), or predicates
(`--idle --def buggy --near 40,30:8`, `--ground|--air|--sea`, `--armed`,
`--engineers`); places are `x,y` in tiles, `@<id>` or `@hq`. `steel help <verb>`
has every option.

- **Units:** `steel move <units> <x,y> [--attack]`, `steel attack <units>
  <target>`, `hold`, `stop`, `route` (a standing patrol), `board`, `pickup`,
  `unload`, `repair`, `assist`, `ability`, `hold-fire`.
- **Hold fire:** `steel hold-fire <units>` stops their automatic fire and
  pursuit while they keep moving and working: slip past a turret line, set an
  ambush, keep a scout unseen. `--off`, or any attack order, releases it;
  `steel show` reads `holding fire`.
- **Priority targets:** `steel priority <id>` marks a visible enemy, and every
  gun of yours that can shoot it from where it stands picks it first: the
  artillery, the radar, the silo. Three marks at a time; `steel priority`
  lists them; `steel priority <id> --off` removes one.
- **Buildings:** `steel build <building> --near <x,y>` (`--deposit <id>` for an
  extractor, `--by <engineers>` to choose who; the nearest idle engineer
  otherwise), `steel upgrade <ids|kind>`, `steel sell <ids>`. A blueprint is
  raised by the engineer you sent; idle engineers help nearby sites by
  themselves.
- **Production and research:** `steel produce <factory> <unit> [-n N]` for a
  one-off, `steel research <node>` (`steel tech` shows what the lab can take).
- **Warheads:** `steel nuke targets` (the best points to aim at, with what each
  destroys and the chance it arrives), `steel nuke queue <silo>`, `steel nuke
  launch <silo> x,y`.
- **Unit actions a mod adds:** `steel show <id>` lists them; `steel
  content-action <units> <module> <action> [--choice id] [--target id] [--at
  x,y]` uses one.

## The brain: `steel auto`

The game's own AI, run by the server for your seat under your fog. Off until
you ask; `steel auto` alone shows what it runs and is doing.

- `steel auto on` hands it the whole seat. `steel direct` takes it back.
- `steel auto --run economy,research` hands it those departments and keeps the
  rest yours: it lays extractors, power, the lab and turrets and climbs the
  tech ladder while production and the army stay in your hands. This is the
  setup to call when the base is falling behind and your attention is needed
  at the front. The departments are `economy`, `research`, `production`,
  `army`, `nukes`, or `all`.
- `--doctrine combined|armored|airpower|naval|turtle|blitz|auto` is the
  personality it fights by; `--favour mbt=2,flak=0` a weight on what it builds
  while it runs `production`; `--posture push|hold|auto` and `--target x,y`
  steer its offensive while it runs `army`; `--reserve 400` metal it leaves
  for your own orders. Every flag changes one thing and keeps the rest, and any
  flag hands it the seat if it was off.
- While it runs the army, any order you give a unit takes that unit out of its
  hands until `steel attach <units>|--all` gives it back; `steel detach` takes
  one without an order; a tactic on `--all` takes only the units in your
  hands.

## Scripts

A **script** is a file of TypeScript or JavaScript run against what your seat
sees every `every` seconds (0.2 at the least), whose orders go out as yours. It
is for a rule no tactic covers: a line kept busy its own way, a raid with a
plan of its own, a department run by code.

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
- `steel lab --vs veteran --script <file> --direct` plays it against the
  built-in AI, many seeds in a minute: prove a script there, between matches.
- A script sees what your seat sees and nothing more, and reaches nothing but
  the match. `tactics/scripts.md` has the patterns and the limits.

## When something does not happen

A refused order says so plainly: `refused, nothing was done: <order>: <why>`,
with the figures (`not enough metal: 135 of its 180 is needed to start, 96 is
free`). Nothing was queued or placed. A standing order that is held back says
why in `steel produce` (`waiting for metal: 281 on hand, 120 needed over the
reserve of 250`); a tactic's refusals show in `steel tactic` and in `wait`.

Where a building may stand is the refusal most worth knowing:

- **The build area.** A side builds on the ground its headquarters hold; only an
  extractor or an offshore rig stands outside it.
- **Production slots.** Each headquarters opens a few, and every factory, yard
  and airbase takes one. With the HQ gone, none can be placed.
- **Frontline.** In that mode the map is sectors, and everything goes up only on
  sectors your side holds and supplies. A sector is taken by armed ground units
  or ships standing at its point, next to ground you already supply; the
  victory points drain the reserve of whoever holds fewer.

If `steel status` says the room's server has no brain for your seat, `steel auto`
falls back to an older autopilot of the CLI's own: `steel help keep`, `mix` and
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
  minutes bank instead of spending: `steel produce stop`, and `steel auto
  --reserve 10000` if the brain runs anything.

## Quick reference

```bash
steel servers                                         # the public rooms
steel connect <address> --code <code> --name <name>   # join; --host-key to host; a public room by its name
steel status                                          # link, phase, seat, economy, who plays the seat
steel brief                                           # the whole situation
steel threats                                         # every enemy group in sight: worth, distance, heading, what it hits, what of yours answers
steel enemies | steel deposits | steel map | steel tech   # drill in
steel defs <unit>                                     # a unit's stats and counters
steel build <building> --near <x,y> | --deposit <id>  # lay a building
steel produce <factory> <unit> -n 2 --repeat | steel produce reserve <metal> | steel produce | steel produce stop
steel rally <factory> <x,y> --join <group>            # new units gather there and join the group
steel research <node> | steel upgrade <ids|kind>
steel move <units> <x,y> [--attack] [--safe] | steel attack <units> <target>
steel group <name> <units> | move <name> <x,y>        # name a group; order it by name
steel tactic guard | retreat --all --below 35 | kite <units> | focus <units> | assault <group> --to x,y   # standing rules; tactic stop <id>|--all
steel path <from> <to> [--air]                        # a route round known guns
steel hold-fire <units> [--off]                       # no automatic fire until released
steel priority [<id> [--off]]                         # mark an enemy for every gun of yours
steel auto on | steel auto --run economy,research | steel direct   # the brain: the whole seat, the base only, off
steel script run <file> | try <file> | stop <name> | log [name] | types   # your own code on the seat
steel nuke targets | steel nuke launch <silo> x,y     # warheads
steel wait <seconds> [--for kinds|none]               # let it run; wakes on what cannot wait, ends on the threats
steel disconnect                                      # leave
steel lab --vs veteran --script <file> --direct       # a script against the built-in AI, headless, between matches
```
