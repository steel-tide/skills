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
- **Raids pay on soft targets.** Four helis sent by hand (`steel move <ids>
  <x,y> --attack`) at lone extractors on islets or at the map's edge cost the
  enemy income for little. Pull them back the moment they meet guns they cannot
  beat, or write the raid as a script that runs from guns by itself.
