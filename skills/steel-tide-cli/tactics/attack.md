# Attacking

- **Mass, then go.** One body of 16 to 20 units with flak alongside takes an
  enemy base in minutes; the same units sent in fours are killed in fours.
  `steel rally … --join army` gathers them, and `steel tactic assault army
  --to <x,y>` marches them as one: the fast wait for the slow, the body fights
  what it meets together, and it falls back by itself when half its worth is
  gone. Set `steel tactic retreat army --below 35` and `steel tactic kite army
  --def arty,mlrs` beside it, and `steel tactic focus army`.
- **Know what you are attacking.** `steel enemies` before every assault: a
  body of light tanks sent at heavy tanks and howitzers comes back as four
  tanks, with the `beaten` line to tell you. Match the mix to theirs
  (`counters.md`), and send a body worth more than what you have seen.
- **Name the target.** `--to` the thing you mean to kill (tiles from `steel
  enemies`), and `steel priority <id>` on that building makes every gun that
  reaches it fire on it first. An assault that reaches its place holds it;
  send the next from there.
- **Production first, then the HQ.** Kill factories, yards and airbases before
  anything else: a faction without production cannot replace what it loses. Then
  the HQ, which usually ends it.
- **Break turret lines from range.** Howitzers outrange turrets; let them shell
  the line while the tanks wait just outside it, then go in: `steel tactic
  assault army --to <a tile 9 tiles short of the turrets>`, then the next
  assault once the turrets fall. Two howitzers on the standing orders before
  a siege.
- **Air goes with the army.** Helis flying ahead of the tanks meet every AA gun
  alone. Put them in the same group and the assault keeps them with the body.
- **Scout before a naval strike.** Ships sent at a coast they cannot see die to
  whatever waits there, and a lost fleet is a coast you cannot defend. A drone
  over the water first; send the fleet only with a clear edge.
- **Several enemies: take them one at a time.** Put everything into the weaker or
  nearer one, finish it, take its ore, then turn. Two half attacks win nothing.
- **Call it off when the base is hit.** An assault body holds no guard at
  home. When `!` lines name the base while the body is out, `steel tactic
  stop <assault id>` and `steel tactic assault army --to @hq`, or keep a
  second group home under `tactic guard`.
- **Hold what you took.** After a base falls, claim its deposits (`steel build
  extractor --deposit <id>`) and leave a turret; otherwise the enemy walks back in.
- **Raids pay on soft targets.** Name the raid (`steel group raid <ids>`),
  send it round the guns you know of (`steel move raid <x,y> --safe`: the
  route keeps clear of every turret and SAM site seen, and says what it must
  pass under), and keep it alive without watching it: `steel tactic kite
  raid` steps it out of any gun's reach, `steel tactic retreat raid --below
  40` sends the hurt home, `steel tactic focus raid` kills one thing at a
  time. Lone extractors on islets or at the map's edge cost the enemy income
  for little.
- **Flank with a route, not a guess.** `steel path @hq <behind their line>`
  prints the legs round their turret wall and names what it cannot avoid;
  `steel path ... --air` minds anti-air alone, for a lift or a wing. A route
  that passes under a Bastion is a route to shell the Bastion first.
