# Attacking

- **Mass, then go.** One body of 16 to 20 units with helis alongside takes an
  enemy base in minutes; the same units sent in fours are killed in fours. The
  brain gathers at its muster and marches in a body (`doing  gathering, next
  push from 12:30`). `steel auto --posture push` sends what stands ready now, so
  push when `steel brief` shows an army worth it; `--posture hold` keeps it home
  until then.
- **Name the target.** Left alone the offensive marches on the nearest enemy
  building it knows. `steel auto --target <x,y>` sends it at the one you choose
  (tiles from `steel enemies`), and `steel priority <id>` on that building makes
  every gun that reaches it fire on it first.
- **Production first, then the HQ.** Kill factories, yards and airbases before
  anything else: a faction without production cannot replace what it loses. Then
  the HQ, which usually ends it.
- **Break turret lines from range.** Howitzers outrange turrets; let them shell
  the line while the tanks wait just outside it, then go in. `--favour arty=2`
  before a siege.
- **Air goes with the army.** Helis flying ahead of the tanks meet every AA gun
  alone. Helis you take by hand go when the tanks go, at the same target.
- **Scout before a naval strike.** Ships sent at a coast they cannot see die to
  whatever waits there, and a lost fleet is a coast you cannot defend. A drone
  over the water first; send the fleet only with a clear edge.
- **Several enemies: take them one at a time.** Put everything into the weaker or
  nearer one (`--target` it), finish it, take its ore, then turn. Two half
  attacks win nothing.
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
