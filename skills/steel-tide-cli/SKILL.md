---
name: steel-tide-cli
description: >-
  Play Steel Tide, a real-time strategy game, through the `steel` command line —
  as the commander of one faction on a dedicated server. Use this whenever asked
  to play, join, or command a Steel Tide match, or to run the `steel` CLI. The
  game's own AI brain plays your seat second by second under your fog; you
  command above it — steer it, take any unit, building or decision into your
  own hands, or turn it off and play by hand and by scripts of your own that
  the session runs every fraction of a second. Covers connecting, reading the
  battlefield, steering the brain, orders by hand, direct control and scripts.
license: MIT
---

# Playing Steel Tide from the command line

Steel Tide is a real-time strategy game about large unit battles. `steel` is its
command line: it joins a dedicated server exactly as the browser game does, and
lets you command one faction.

A match runs at 30 ticks a second and does not pause for you, so you are never
the only controller of your seat. There are three, on the same game:

- **The brain** plays every second. It is the game's own AI — the veteran's build
  order, economy, tech, production, defence and attack waves, line for line —
  run by the server for your seat. It takes the seat when the match starts.
- **You** command above it. You steer it (`steel auto`), and you give any order
  by hand whenever you judge better than it does. An order you give a unit takes
  that unit out of the brain's hands until you give it back (`steel attach`).
  Buildings you lay, units you queue and research you start are counted by the
  brain as its own.
- **Your scripts** play at the brain's speed with your judgement: code you
  write, run by the session every fraction of a second against what your seat
  sees (`steel script`). A rule for one kind of unit, a department taken from
  the brain, or with the brain off (`steel direct`) the whole seat.

The built-in AI comes as four personas. Relaxed, Standard and **Seasoned** fight
under the fog exactly as you do; Seasoned plays the Veteran's numbers, and it is
what your brain plays as, so against Seasoned your seat plays a mirror. The
**Veteran** alone sees through the fog of war, and its guns fire at whatever
they reach, seen or not. So your brain alone against a Veteran loses more often
than it wins; what wins is you. Your edge is judgement the brain
does not have: finding out what the enemy is doing and answering it — scouting, a
radar, the right counters, where and when to strike, SAM sites before the
warheads come, not standing in the reach of artillery you cannot see. Look,
decide, steer, and step in by hand where it matters.

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
| something must happen faster than your turns: write a script | `tactics/scripts.md`   |

## Setup

The `steel` binary is installed separately (`curl -fsSL https://steelti.de/cli.sh | sh`).
Confirm it is on PATH:

```bash
steel --version
```

Connect to a server. The default game port is 28785; you need the join code the
host set (ask the user for the address and code if you do not have them):

```bash
steel connect <address> --code <join code> --name <a name>
```

To play on the game's own public servers (the rooms its Browse page lists,
Breakwater, Ironclad and the rest), list them and join one by its name; they
need no code:

```bash
steel servers                          # each room: seats, status, location, ping
steel connect <server> --name <a name>
```

Choose a `recruiting` or `free` room, or a `lobby` with company. A `playing`
room seats you in the stands until its match ends, and a room marked
`another version` or `unreachable` cannot be joined.

To avoid retyping the address and code, save them once (written to a 0600
config file) and connect by name thereafter:

```bash
steel config server home <address> --code <join code> --as <a name>
steel connect home
```

Then poll until the match starts (the host starts it):

```bash
steel status
```

When `phase` reads `playing`, you are in — unless `seat` reads `spectator`: a
match was already on when you connected, so you are watching it from the stands
and will be seated when the lobby returns (poll `steel status` until `phase`
reads `lobby`, then `ready`). If the user asked you to **host**,
connect with `--host-key <key>` instead, then set up and start the match:

```bash
steel lobby slot 1 ai:veteran && steel start
```

If no host is connected, the seated player who has been in the room longest is
the **acting host** and may do the same — `steel status` says `(acting host)`
beside the server when that is you. Then `steel lobby` to see the seats, close
or fill the empty ones (`steel lobby slot <i> ai:standard` or `closed`), and
`steel start` once everyone seated is ready.

To find strangers for the open human seats, `steel lobby recruit` lists the
room as recruiting for five minutes (renewable once a minute). `steel spectate
--player <name>` moves a player to the stands (`--off` brings them back). The
host can also choose the acting host: `steel lobby host <name>` hands the room
to a seated player, and `steel lobby host none` takes it back. If the host
does this to you, `steel events --kind lobby` says `you run the lobby as acting
host` or `you went to the stands`.

The host can set the unit cap with `steel lobby set --pop 300`: 50 to 300 in
steps of 10, with 100 as the default.

`steel lobby` shows the match being set up: its **mode** — conquest, or
**Frontline** (the map is sectors, and you build only on the ones your side
holds and supplies; see *When something does not happen*) — and the **time
limit** most rooms have (60 minutes by default), the production slots per HQ,
the tech tree and the build radius. Running the room, you set them with
`steel lobby set --mode frontline --time-limit 45` and the rest of `steel help
lobby`. Where the room's mods bring factions, `steel lobby` lists them and you
take one with `steel lobby slot me --faction <id>`.

A server may require mods, or run a world (a scenario and its content);
`steel` fetches mods from the public registry and checks the world by itself.
If the connect fails saying a mod could not be got or the world could not be
verified, the host has to update the server.

You can steer the brain before the match starts (`steel auto --doctrine armored`
in the lobby): it takes the seat with that steer at the whistle.

## The command loop

1. **Look.** `steel brief` is the whole picture on one screen: economy, base,
   army, the enemy you know of, deposits, recent events, and what the brain is
   doing (the `brain` and `doing` lines: its doctrine, its offensive or its next
   push, its objective, how many enemy buildings it knows). A loss names what
   killed it (`lost mbt x3 at 40,30 to mlrs x2`), even when the killer is out of
   sight: a gun you cannot see is still named. Drill in with
   `steel enemies`, `steel deposits`, `steel map`, `steel tech`, `steel defs <unit>`.
2. **Decide.** Is the brain doing what the situation needs? Most of the time it
   is. When it is not — it cannot see the artillery shelling the base, it is
   building tanks against an air force, its offensive is going at the strong end
   of the enemy's base — steer it, or do the thing yourself.
3. **Steer or act.** `steel auto` changes what the brain does from its next
   second; a hand order does one thing now (below).
4. **Wait.** `steel wait 45` lets the match run (in game-time seconds) and
   returns the moment something that cannot wait happens: the base under
   fire, a loss, a warhead, a faction out. Woken so, it ends on the **threats**
   as they stand — who is coming, from where, how fast, what they have under
   their guns, what of yours answers — so you can act without another look.
   Its last line is your economy now. A fight it has already shown you does
   not wake it again at every shot; a new place or a building lost does.
   `--for produced,built` names your own kinds; `--for none` is a plain timer.
5. Repeat, adapting as the enemy reveals itself.

**You are never the last to know.** Every reply — an order, a list, even a
refusal — opens with `!` lines for what fell since you last looked (`brief`,
`wait`, `events`, `threats`): read them before the rest. `steel threats` is
the alarm read in full at any time.

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
  favoured above 1 is always built at least a little, even if the brain has not
  seen what calls for it, and one it would never build takes your figure as its
  weight, where a line of yours can make it. This is how you answer what you have seen: `counters.md`
  says what beats what.
- `--posture push|hold|auto` — `push` sends whatever stands ready at its
  objective now and keeps sending; `hold` never marches (it still defends);
  `auto` is its own clock.
- `--target x,y` — what its offensive marches on, in place of the nearest enemy
  building it knows (`none` to clear). Point it at the enemy's weak side, their
  outlying extractors, the silo.
- `--reserve 400` — metal it leaves on the balance for your own orders. The brain
  spends as it earns, so a radar or a SAM site you lay by hand waits for a moment
  it happens to leave the metal lying; set a reserve first, lay it, then set it
  back to 0.
- `steel auto off` takes the whole seat back: every order is yours.
  `steel auto on` hands it over again.

## Orders by hand

Anything the brain would do, you can do yourself, at any moment:

- **Units:** `steel move <units> <x,y> [--attack]`, `steel attack <units> <target>`,
  `hold`, `stop`, `route` (a standing patrol), `board`, `pickup`, `unload`,
  `repair`, `assist`, `ability`, `hold-fire`. The unit is then yours: the brain
  leaves it alone until `steel attach <units>` (or `--all`) gives it back.
  `steel detach <units>` takes units without an order. `steel brief` counts what
  you hold (`hands`).
- **Groups:** `steel group raid 104,105,107` names a group (or by predicates:
  `steel group raid --def drake --near 70,10:12`). Then `move raid 76,3`,
  `attack raid 40`, `units raid`, `--group raid` anywhere, `tactic focus raid`,
  and `s.group('raid')` in a script. The dead fall out by themselves. Never
  hard-code ids in a script again.
- **Routes:** `steel path <from> <to> [--air|--sea]` finds a way round every
  enemy gun you know of (buildings seen, units seen in the last minute) and
  says what it keeps clear of and what it has to pass under. `steel move <units>
  <x,y> --safe` sends them along it, leg after leg. This is how a raid reaches
  the extractors behind a turret wall, and how a lift skirts the SAM sites.
- **Tactics:** the micro you would otherwise script, kept by the session four
  times a second: `steel tactic guard` (what comes within 24 tiles of the base
  is met by the units in your hands, who then go back where they stood),
  `steel tactic kite --def arty,mlrs` (a battery a gun can reach steps out of
  its reach, and back to the brain once clear), `steel tactic retreat --all
  --below 30` (a hurt unit goes home, and is let go once mended), `steel tactic
  focus raid` (the guns on the weakest thing in reach). `steel tactic` shows
  what each holds; `steel tactic stop <id>|--all`. A tactic's order is a hand
  order; on `--all` while the brain runs the army it takes only units already in
  your hands — `--def`, a group or ids take units from the brain. **Set `tactic
  guard` at the whistle**: it is what makes a raid you took by hand, or a seat
  you play by hand, defend itself while you think.
- **Hold fire:** `steel hold-fire <units>` stops their automatic fire and
  pursuit while they keep moving and working: slip past a turret line, set an
  ambush, keep a scout unseen. `--off`, or any attack order, releases it;
  `steel show` reads `holding fire`. It is a hand order like the rest.
- **Priority targets:** `steel priority <id>` marks a visible enemy unit or
  building, and every automatic weapon of your faction that can shoot it from
  where it stands, the brain's included, picks it first. It is not an order to
  any unit, so nothing leaves the brain's hands: it is how you point the brain's
  guns at the artillery, the radar or the silo. Three marks at a time (a fourth
  replaces the oldest); `steel priority` lists them, as `steel brief` does, and
  `steel priority <id> --off` removes one, even out of sight.
- **Buildings:** `steel build <building> --near <x,y>` (a radar, SAM sites, a
  turret where the brain did not put one), `steel upgrade <ids>`, `steel sell <ids>`.
  The brain crews your blueprints and counts them toward its own plans.
- **Production and research:** `steel produce <factory> <unit> [-n N]`,
  `steel research <node>`. They go in the queue beside the brain's.
- **Warheads:** `steel nuke targets` (the points the brain itself would aim at), `steel nuke launch <silo> x,y`. The brain
  launches too while it runs `nukes`; leave that out of `--run` to choose every
  target yourself.

`steel help <verb>` has every option. Units are ids like `12,15` or predicates
like `--idle --def buggy --near 40,30:8`; places are `x,y` in tiles, `@<id>` or `@hq`.

A match whose content declares capability modules gives some units actions of
their own: `steel show <id>` lists each one's module, action and choices, and
`steel content-action <units> <module> <action> [--choice id] [--target id] [--at x,y]`
uses it. The server checks it like any order (ownership, sight, stock,
cooldown); a refused action changes nothing.

## Direct control and scripts

`steel direct` turns the brain off: nothing plays the seat but your orders and
your scripts. `steel auto` hands it back.

A **script** is a file of TypeScript or JavaScript; the session runs it every
`every` sim seconds (0.2 at the least) against what your seat sees and sends
the orders it gives, checked like the verbs and judged by the server like any
click. It is how a rule holds between your turns: a battery pulled out of
reach, a group taken out from under guns it cannot see, a line kept busy.

```ts
export default {
  every: 0.5,
  tick(s) { /* the whole seat: s.units(), s.enemies(), s.events, s.memory ... */ },
  rules: {
    arty(u, s) {  // once a run for each of your units of this kind; also 'army' and '*'
      const close = s.enemies({ near: u, within: 6, armed: true, seen: 0 });
      if (close.length) s.move(u, s.away(u, s.nearest(u, close)!, 5));
    },
  },
};
```

- `steel script types > steel.d.ts` prints the whole API. The seat: `time`,
  `metal`, `free` (metal not yet promised), `income`, `power`, `pop`, `popCap`,
  `hq`, `base`, `map`, `spawns`. Reading: `units`, `buildings`, `enemies` (with
  `seen`, seconds since in sight), `deposits`, `def`, `events` (the journal
  since the last run, losses with their killers), `memory` (kept between runs),
  `brain`, `visible`, `explored`, `ground`, `water`, `dist`, `nearest`, `away`,
  `toward`. Orders: `move`, `attackMove`, `attack`, `stop`, `hold`, `holdFire`,
  `ability`, `build`, `produce`, `research`, `upgrade`, `sell`, `repair`,
  `board`, `unload`, `rally`, `arm`, `launch`, `attach`, `detach`, `steer`,
  and `log`.
- `steel script try <file>` runs it once now and prints the orders it would
  give, sending nothing. `steel script run <file>` runs it (again with the same
  name replaces it); `steel script` shows every script's runs, orders, refusals
  and errors; `steel script log [name]` what it said and why an order was
  refused; `steel script stop <name>|--all`.
- `steel lab --vs veteran --script <file>` (add `--direct` for a whole seat)
  tests it headless against the built-in AI, many seeds in a minute: prove a
  script there before a live match.
- A unit a script orders is out of the brain's hands until `s.attach` gives it
  back, as with any hand order. `s.steer({ run: [...] })` hands the brain only
  the departments you name: take `army` and play it by script.
- Limits: 100 ms a run; five failed runs in a row stop the script; 60 orders a
  run, the rest dropped; the same order to the same units is not sent again for
  3 s; all scripts together send 30 orders a sim second. A script sees what your
  seat sees and nothing more, and cannot touch files or the network.

## When something does not happen

A refused order says so plainly: `refused, nothing was done: <order>: <why>`,
with the figures (`not enough metal: 135 of its 180 is needed to start, 96 is
free`). Nothing was queued or placed.

Where a building may stand is the refusal most worth knowing:

- **The build area.** A side builds on the ground its headquarters hold; only
  an extractor or an offshore rig stands outside it (`too far from a friendly
  headquarters`).
- **Production slots.** Each headquarters opens a number of slots (three
  unless the room says otherwise), and every factory, yard and airbase takes
  one: `no free production slot: build another headquarters for more`. With
  the HQ gone, none can be placed.
- **Frontline.** In a room on Frontline the map is divided into sectors, and
  everything, extractors too, goes up only on sectors your side holds and
  supplies (`outside your supplied ground`); a headquarters may go on any sector
  you hold. A sector is taken by armed ground units or ships standing at its
  point (aircraft do not count), next to ground you already supply; it is
  supplied while a chain of your sectors links it to a headquarters. The
  victory points drain the reserve of whoever holds fewer: at zero that side is
  out. The brain
  sends squads to the points by itself; `steel` does not draw the sectors, so a
  build there by hand goes near what you already hold.

If the brain seems stuck, look at `steel auto` and `steel brief` first: `no enemy
known yet: scouting` means it has found nothing to march on (scout for it, or give
it a `--target`); `gathering … next push from 12:30` means it is waiting to march
in a body (`--posture push` sends it now); a department you left out of `--run` is
waiting on you.

## Legacy: the CLI's own autopilot

A server from before the brain does not know the order; the CLI then runs its
own older autopilot in the seat instead, and says so (`steel status`). `steel
auto --legacy` asks for it outright. Its standing intents are `steel keep
<building> <n>` (build goals), `steel mix <line> unit=weight` (production),
`steel force <name> --size N attack auto|x,y|#id` (army groups), `steel expand
<deposit>`, `steel plan` and `steel scout on`. Under the brain those verbs do
nothing and say so: `keep` is a hand `build`, `mix` is `auto --favour`,
`force … attack` is `auto --target` or `--posture push`, or a hand
`move --attack` for a group of your own.

## Rules

- Use only `steel` verbs, and the script files you write for `steel script`.
  Do not edit the game's files, restart the server, or read the server's files.
  You see only what a player sees (fog of war is real).
- Every output is plain text; add `--json` to any command for structured data.
  Exit 0 means done, 1 means refused (the message says why), 2 usage, 3 no
  session or the server is gone.
- One word per argument: zsh does not split an unquoted `$var`, so
  `$S build $what` hands `steel` "radar --near 40,30" as one argument. Use
  `${=what}`, an array, or separate words.
- Keep going until `steel status` shows phase `ended` or you are eliminated. Then
  `steel disconnect` and report the result, how the match turned, and what you
  would change.
- Under a time limit, the faction still standing with the most unspent metal
  wins at the deadline, and equal balances across teams are a draw. In the last
  minutes bank instead of spending: `steel auto --reserve 10000` (its most)
  keeps the brain's hands off the balance.

## Quick reference

```bash
steel servers                                         # the public servers, as the game's Browse page lists them
steel connect <address> --code <code> --name <name>   # join; --host-key to host; a public server by its name
steel status                                          # link, phase, seat, economy, the brain
steel brief                                           # the whole situation, and what the brain is doing
steel threats                                         # every enemy group in sight: worth, distance, heading, what it hits, what of yours answers
steel enemies | steel deposits | steel map | steel tech   # drill in
steel defs <unit>                                     # a unit's stats and counters
steel auto [on|off] [--run depts] [--doctrine D] [--favour u=w,...] [--posture push|hold|auto] [--target x,y|none] [--reserve M]
steel move <units> <x,y> --attack | steel attack <units> <target>   # take units into your hands
steel hold-fire <units> [--off]                       # no automatic fire until released
steel priority [<id> [--off]]                         # mark an enemy for every gun of yours; nothing leaves the brain
steel attach <units>|--all | steel detach <units>     # give them back; take them without an order
steel group <name> <units> | move <name> <x,y>        # name a group; order it by name
steel tactic guard | kite <units> | retreat <units> --below 30 | focus <units>   # standing micro the session keeps; tactic stop <id>|--all
steel path <from> <to> [--air] | move <units> <x,y> --safe   # a route round known guns, and units sent along it
steel direct                                          # the brain off: your orders and your scripts alone
steel script run <file> | try <file> | stop <name> | log [name] | types   # your own code on the seat
steel build <building> --near <x,y> | steel produce <factory> <unit> | steel research <node>
steel nuke targets | steel nuke launch <silo> x,y     # warheads
steel wait <seconds> [--for kinds|none]               # let it run; wakes on what cannot wait, ends on the threats
steel disconnect                                      # leave
steel lab --vs veteran [--script <file>] [--direct]   # your seat (and scripts) against the built-in AI, headless
```
