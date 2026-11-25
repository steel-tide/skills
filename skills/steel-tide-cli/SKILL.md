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
- **Standing production** (`steel produce … --repeat`): a factory kept
  turning out a unit for the rest of the match, one at a time, never
  spending under the reserve you set for buildings.
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
   `produce --repeat`, `rally --join`, `tactic`, and `--when-affordable` on
   what the metal is not there for yet. Prefer standing orders: a
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
  there as one body on a route round the turrets you know of. The reply says
  the route: its length against the straight line, the legs, the guns it keeps
  clear of and the ones it passes under. It does not go round the guns at the
  place itself (it goes to fight them), and if the way round is more than half
  again the short way it takes the short way and says so. A unit more than
  `--spread` (6, more in a big body) ahead of the body's middle waits; what
  the body meets it closes on together; at the place it holds like a guard
  until you stop it or send a new assault; and when it has lost half the most
  it was worth (`--below 50`) it falls back to where it set out and tells you
  (`assault3: beaten at 29,36: 6 units worth 720 left of 1680; falling back to
  19,17`). The middle is where most of the body stands. `steel tactic` names
  the units coming up from behind; a slow one on the move is waited for. A
  unit that stands still off the body for 25 s is left behind, with a line
  saying so: it is walled in or cut off, and no order reaches it. A unit the
  retreat sent home comes back up once mended. Setting out, reaching, leaving
  units behind and falling back are `contact` lines, which wake `steel wait`.

Rules on one unit run in an order — retreat, kite, assault, guard; focus only
picks targets — so `tactic retreat army`, `tactic kite army --def arty` and
`tactic assault army --to x,y` on the same group are one attack: the hurt go
home, the batteries keep their distance, the body marches.

### Production that keeps going

```bash
steel produce factory mbt -n 2 --repeat     # this factory turns out mbt for the rest of the match
steel produce factory flak --repeat         # and flak: the two take turns, two mbt to each flak (-n is the share)
steel produce reserve 500                   # refills never spend the last 500: that is for your buildings and research
steel produce                               # the standing orders, and what holds each back (metal, the unit in work)
steel produce stop [factory]                # end them (all, or one factory's)
steel produce hq engineer -n 2 -w           # a one-off bought when it can be paid, ahead of the standing orders
```

A factory with an empty queue is the most common way an agent loses: it looks
every thirty seconds and the queue ran dry in ten. Set the standing orders at
the whistle and change them when `steel enemies` changes (`counters.md`).

The session keeps one unit in work at each factory and queues the next in
its last seconds, so the factory never stands. **Every unit on a queue holds
its price as committed metal**, and an order by hand needs the metal that is
not committed. A queue stacked with the mix held all of it: agents with 1,500
on hand had every building refused. So never stack a queue by hand
for throughput. A second factory is throughput; a deep queue is only
committed metal. A refusal for metal and the brief's economy line say what
the committed metal is promised to (`1019 committed to factory#139 queue (mbt)
280, 2 sites going up 245`). With `-n` and no `--repeat`, what the metal
covers is queued and the reply says how many that was.

### Buying when the metal is there: the spend list

```bash
steel upgrade extractor --when-affordable   # every extractor raised a level whenever it can be paid, for the rest of the match
steel build warlab --near @hq -w            # one building, placed the moment it can be paid (-w is --when-affordable)
steel research deepDrilling -w              # one tech node, queued the moment it can be paid
steel nuke queue silo -w                    # a warhead, started the moment its 2,500 is there
steel spend                                 # the list, top first: what each entry buys and what it waits for
steel spend first 3                         # entry 3 is bought before the rest
steel spend stop 3 | steel spend stop --all # end entries
```

Never ask the same `build`, `upgrade`, `research`, `produce` or `nuke queue`
again each turn until the metal is there, and never juggle `produce reserve` to
save for something: add `--when-affordable` and the session orders it the second it
can be paid. A refusal for metal says so itself. The list is read from the top
once a second, one purchase at most:

- An entry short of nothing but metal is **saving**, and the entries after it
  wait. Order the list by what matters most (`spend first <n>`).
- An entry something else holds back is **held** and passed over until that
  clears: tech not met, no spot, no engineer. `steel spend` says which.
- **A single purchase goes ahead of the factories.** A building, a tech node,
  buildings named by id (`steel upgrade 21,22 -w`), units or a warhead: standing production
  leaves its price alone, so a queue cannot starve it. Put nothing dear on the
  list that you would not slow the factories for. A building stays listed
  until its site stands, and is ordered again if its engineer never gets there.
- **A standing upgrade goes behind them.** `steel upgrade <name> -w` stays for
  the match: every building of that line you have or get, the cheapest step
  first, up to the top level (`upgrade extractor -w` ends with every extractor
  a Deep-Core; `--def extractor` stops at the first level). It stands back
  while a factory with standing production stands idle, and buys while they
  are at work, so it never leaves you without an army in a rush. If it never
  gets a turn your income does not carry both: stop a standing order, or name
  the extractors by id. It also waits while power is short: an upgrade draws more,
  so keep `steel build power --near @hq -w` ahead of it.
- `steel brief` carries the list on its `spend` line.

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
  A building's name is its line at any level: `factory` still means it once it
  is a `factory2`, so `produce factory mbt` and `rally factory` keep working
  after an upgrade (two of the line: name the id). `--def factory` is the
  plain ones alone.
- `steel path <from> <to> [--air|--sea]` finds a way round every enemy gun you
  know of and says what it keeps clear of and what it has to pass under. `steel
  move <units> <x,y> --safe` sends them along it. This is how a raid reaches
  the extractors behind a turret wall, and how a lift skirts the SAM sites. An
  enemy unit counts only if seen in the last 10 seconds, and only as a step
  aside: it has moved on by the time the route is walked.

## Orders by hand

Units are ids (`12,15`), a group's name (`raid`), a kind (`mbt`), or predicates
(`--idle --def buggy --near 40,30:8`, `--ground|--air|--sea`, `--armed`,
`--engineers`); places are `x,y` in tiles, `@<id>` or `@hq`. `steel help <verb>`
has every option.

- **Units:** `steel move <units> <x,y> [--attack]`, `steel attack <units>
  <target>`, `hold`, `stop`, `route` (a standing patrol), `board`, `pickup`,
  `unload`, `repair`, `assist`, `ability`, `hold-fire`. An enemy building you
  know of but do not see now cannot be shot at by id: `steel attack <units>
  <id>` then sends the units to where it stood on an attack-move and says so,
  and `@<id>` is that place (`move raid @57`). Artillery that must shell it
  from range takes the tile: `steel attack <units> x,y`.
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
  themselves. `--near` finds the nearest spot the engineer can reach that
  leaves a way round your buildings and off the ground your factories' units
  form up on; read the `note:` under the order when it says the spot is far
  or may close a gap. `upgrade` starts every one the metal covers, the cheapest
  first, and says which it left; any of the three takes `--when-affordable`.
- **Production and research:** `steel produce <factory> <unit> [-n N]` for a
  one-off, `steel research <node>` (`steel tech` shows what the lab can take).
- **Warheads:** `steel nuke targets` (the best points to aim at, with what each
  destroys and the chance it arrives), `steel nuke queue <silo> [-w]`, `steel nuke
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
  would change. `steel wait` on a match that is over answers at once with the
  result, so a loop on it ends there.
- `steel chat <text>` talks to everyone, `--team` to your side. A line carries
  240 characters: a longer one is sent up to the last whole word that fits and
  the reply quotes what did not go, to send as the next line.
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
steel produce <factory> <unit> [-n share] --repeat | steel produce reserve <metal> | steel produce | steel produce stop
steel rally <factory> <x,y> --join <group>            # new units gather there and join the group
steel research <node> | steel upgrade <ids|kind>      # these, build, produce and nuke queue take --when-affordable (-w): bought when it can be paid
steel spend | steel spend first <n> | steel spend stop <n>|--all   # the spend list: what waits for metal, top first
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
