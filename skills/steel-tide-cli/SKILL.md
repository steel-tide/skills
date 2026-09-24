---
name: steel-tide-cli
description: >-
  Play Steel Tide, a real-time strategy game, through the `steel` command line —
  as the commander of one faction on a dedicated server. Use this whenever asked
  to play, join, or command a Steel Tide match, or to run the `steel` CLI. You set
  the strategy (economy, production, forces, attacks); an autopilot fights the
  battle. Covers connecting, reading the battlefield, and commanding by standing
  intent at your own pace.
license: MIT
---

# Playing Steel Tide from the command line

Steel Tide is a real-time strategy game about large unit battles. `steel` is its
command line: it joins a dedicated server exactly as the browser game does, and
lets you command one faction. You do **not** micro-manage units. You make the
macro decisions a human commander makes — what to build, what to produce, where
to attack — and a built-in autopilot carries them out every second: it runs the
economy, expands, defends the base, and moves your armies in formation.

This matters because you cannot act in real time: a match runs at 30 ticks a
second and does not pause for you. So everything you command is a **standing
intent** that code re-resolves continuously. A decision that lands ten seconds
late is still carried out correctly, and the seat is never idle while you think.
Command in bursts, then let time pass with `steel wait`.

This file is the verbs and the loop. How to win with them is in `tactics/`, one
chapter per part of the game. Read `tactics/opening.md` before every match, then
load the chapter that matches what is happening; do not load them all.

| When                                                         | Load                   |
| ------------------------------------------------------------ | ---------------------- |
| a match is starting: reset, read the map, build order, timings | `tactics/opening.md`   |
| income, expansions, upgrades, a goal or line not spending     | `tactics/economy.md`   |
| the enemy fields something new: what beats it                 | `tactics/counters.md`  |
| the base is threatened or under attack                        | `tactics/defense.md`   |
| planning or running an offensive                              | `tactics/attack.md`    |
| the enemy is across water (`reach` says so)                    | `tactics/naval.md`     |
| a radar, reactor or silo on either side                        | `tactics/warheads.md`  |
| losing: army gone, HQ gone, a warhead landed                  | `tactics/recovery.md`  |

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

To avoid retyping the address and code, save them once (written to a 0600
config file) and connect by name thereafter:

```bash
steel config server home <address> --code <join code> --name <a name>
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
`steel start` once everyone seated is ready. The console and the server's save
stay with the host key.

A server may require mods (units and buildings added by players). `steel` handles
that by itself: refused at the door, it fetches each required mod from the public
registry, applies it and connects again, so `brief`, `show` and your orders know the
mod's units by their ids like any other. If the connect fails saying a mod could
not be got, the server is on a version the registry no longer serves; tell the user
the host has to update it. (`steel defs`, which runs outside the session, lists the
game's own units only.)

No server to hand? Judge the autopilot headless against the built-in AI, which
needs nothing else:

```bash
steel lab --vs veteran
```

## The command loop

1. **Look.** `steel brief` is the whole picture on one screen: economy, base,
   your army and forces, the enemy, deposits, recent events, and your standing
   intents. Drill in with `steel enemies`, `steel deposits`, `steel map`,
   `steel ops`, `steel defs <unit>`.
2. **Decide and command.** Set standing intents (below). Each prints what it did,
   or why it was refused.
3. **Wait.** `steel wait 45 --for contact,lost` lets the match run (in game-time
   seconds) and returns early when a force makes contact, you lose something, or
   the match ends. Its last line is your economy now. This is your main source of
   news — you usually do not need another `brief` until something changes.
   Other kinds worth waiting on: `attack` (the base is hit), `destroyed` (an
   enemy building gone or a unit killed in sight), `nuke`. A line that repeats
   is folded into one with a count.
4. Repeat, adapting as the enemy reveals itself.

## Commands you give

These are standing intents the autopilot carries out. Run `steel help <verb>` for
options.

- `steel plan set reserve=80 engineers=3 retreatAt=0.4` — the standing policy.
  `steel plan` prints it with every build goal numbered and what each is doing.
- `steel keep extractor 8` / `steel keep cannonturret 2 --front` — buildings to
  keep standing, worked in list order, rebuilt when lost. Placement is the
  autopilot's problem. One rule: a goal is a building plus a place (base,
  `--front`, `--rear`, or an `--at x,y` tile).
  - `keep B n` sets the final count of B there: its last step becomes n, and
    earlier steps of it are capped at n. `keep` prints what it changed.
  - `keep B n --then` adds a later step instead, for a build order:
    `keep extractor 4`, `keep factory 1`, `keep extractor 7 --then`.
  - `keep B 0` removes B's goals: at that place if you name one, everywhere if
    you do not. `--first` and `--before <step|building>` reorder.
  - `--save` makes a goal save up: while it is the next saving goal to buy, its
    price is held back from production and from every goal after it. Without
    it, a cheap goal goes ahead while a dear one waits for metal.
  - An upgrade level (`extractor2`, `factory3`, `samsite`) keeps that many
    standing at that level or above, building the base building first when
    short. A warhead (`keep warhead 2`, `keep emp 1`) keeps that many in the
    magazines and always saves for them.
  - A goal whose tech is missing gets the missing buildings put in front of it
    (a silo needs a radar and a reactor); `steel plan` shows them as `+` steps.
- `steel mix factory mbt=3 flak=1 arty=1` — what a production line makes, by
  weight; it never idles while metal lasts. Lines: `factory`, `airbase`, `navyard`.
  `arty=1.5:8` caps artillery at 8 standing and queued, so one line cannot fill
  the unit cap. The reply says what each line can make of the mix now and what
  waits on an upgrade (`mbt needs factory2`) or tech; when half a line's mix
  waits on its next level, the plan raises the line itself.
- `steel expand <deposit>` — claim a metal deposit. The builder is whoever can
  get there: an engineer on the same ground, an engineer boat on the water that
  washes it (a navy yard builds one when needed), or an engineer flown over by a
  transport. `steel deposits` lists them by id with a `reach` column.
- `steel force <name> --size 14 --mix mbt,flak,arty attack auto` — an army group
  that gathers to size, advances as one body, engages, retreats when it is losing,
  refills, and attacks again. `attack auto` takes the nearest known enemy; give
  `x,y` or `#id` to name a target, and a building named that way is taken from
  wherever each unit's guns reach it (ships from the water in range). With no
  `--mix` a force takes what can reach and hit the target: guns that cannot hit
  the ground (flak, fighters, missile boats) ride along as escorts, one to every
  four; ground units across water are carried over in transports when there are
  any (`plan set lifts=on`, the default). Also `defend <x,y>` (answers anything
  inside the post, engineers and blueprints included, each threat with the guns
  that can hit it), `harass` (raid soft targets, flee from guns), `idle`,
  `disband`. Units no force has room for wait at home as the reserve, which
  answers attacks on the base; `steel forces` shows it.
- `steel scout on` — keep a cheap scout (a drone or a buggy, never a dear unit)
  revealing the map. It rests after each loss and keeps clear of where the last
  one fell.

Read how they are going with `steel ops` (intents, every goal's state, what each
line makes, orders the autopilot had refused) and `steel forces`. You rarely
order single units. When you do (`move`, `attack`, `board` ...), the autopilot
leaves those units alone until they have stood idle for 20 seconds, so a force
does not pull them back mid-errand; `steel detach <ids>` keeps them out until
`steel attach <ids>`. `steel auto off` hands the seat fully back to manual
one-unit orders (`steel help` lists them).

## When something does not happen

Every goal has a state in `steel plan` and `steel ops`: done, working (rising,
upgrading), saving (with how much of the price is put by), held (metal: needs
1,800, 1,210 spendable; tech: needs radar and reactor standing), blocked (the
ground: outside the headquarters' build area, no shore, no free deposit; it
tries again after a minute), or waiting (for a free engineer, for power). Read
it before you change the plan: the fix is usually in the note.

A refused order says so plainly: `refused, nothing was done: <order>: <why>`,
with the figures (`not enough metal: 135 of its 180 is needed to start, 96 is
free (436 on hand, 340 committed); the autopilot committed 340 in the same
second`). Nothing was queued or placed. `build --near` names the rule the ground
ran into (not enough metal, outside the build area, no free deposit) rather
than saying there is no room.

## Across water

When the enemy starts on another landmass the autopilot plays the island plan
(while you have not changed the plan): a navy yard and an airbase first,
engineer boats and airlifted engineers for the islets, transports for the
army. `steel brief` says so on its `reach` line. What can hurt an island base:
aircraft, ships within range of the shore (a force sends each ship to the water
in range of its target), and ground units landed by a lift. Ground units alone
at home are a garrison: cap them with `mix factory ltank=2:6` and put the rest
into the navy yard and the airbase.

## Warheads

A nuclear warhead costs 2,500 and 180 s, needs a radar and a reactor standing,
and a silo holds three; an EMP costs 1,200 and needs a radar alone. A warhead
flies as an aircraft: **SAM sites** (upgraded AA turrets: `keep samsite 2`)
always shoot at it, other anti-air only at rank 2 or above, and the
interceptor never does (it stops rockets). `steel keep warhead 2` keeps the
magazines stocked and saves up for them; `steel nuke targets` ranks what you
know of the enemy by what a blast there would take, with the SAM sites near
each aim; `steel nuke launch <silo> x,y` fires. `steel wait --for nuke` wakes
on a launch (theirs names the launchers you know; where it will land is never
told to anyone), an interception, an impact, and the strike report a few
seconds after yours lands (what was destroyed, damaged, or is hidden by fog).

## Rules

- Use only `steel` verbs. Do not edit files, restart the server, or read the
  server's files. You see only what a player sees (fog of war is real).
- Every output is plain text; add `--json` to any command for structured data.
  Every list verb gives an array of rows. `units`, `buildings` and `enemies`
  share their fields (`id`, `def`, `tile`, `hp`, `owner`); an enemy row adds
  `kind` and `seen` (0 in sight now, else seconds since, `hp` as it was then).
  `deposits` rows carry `status` and `reach`, `forces` rows `state` and `size`.
  Exit 0 means done, 1 means refused (the message says why), 2 usage, 3 no
  session or the server is gone.
- One word per argument: zsh does not split an unquoted `$var`, so
  `$S keep $goal` hands `steel` "extractor 4" as one argument. Use `${=goal}`,
  an array, or separate words.
- Keep going until `steel status` shows phase `ended` or you are eliminated. Then
  `steel disconnect` and report the result, how the match turned, and what you
  would change.

## Quick reference

```bash
steel connect <address> --code <code> --name <name>   # join; --host-key to host
steel status                                          # link, phase, seat, economy
steel brief                                           # the whole situation
steel enemies | steel deposits | steel map            # drill in
steel defs <unit>                                     # a unit's stats and counters
steel keep <building> <count> [--front|--rear] [--then] [--save]   # standing build goals
steel keep warhead 2                                  # keep the silos stocked
steel mix <line> <unit>=<weight>[:max] ...            # production, by weight
steel expand <deposit>                                # take a metal deposit
steel force <name> [--size N --mix a,b] <mission>     # attack auto|x,y|#id | defend x,y | harass | idle
steel scout on                                        # keep the map lit
steel plan | steel ops | steel forces                 # the goals, and how your intents are going
steel detach <ids> | steel attach <ids>|--all         # out of the autopilot's hands, and back
steel nuke targets                                    # where a warhead does the most
steel wait <seconds> [--for contact,lost,attack,nuke] # let it run, report back
steel disconnect                                      # leave
steel lab --vs veteran                                # headless self-test, no server
```
