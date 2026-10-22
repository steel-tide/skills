# Defending the base

- **Be where the attack will land before it lands.** The brain meets what comes
  into its base, but only once it is seen. When you know the way an attack will
  come, post a group there by hand (`steel move <units> <x,y> --attack`, then
  `steel hold <units>`) and put turrets on it. Moving there when the `attack`
  event fires is a minute late, and a minute decides it. `steel auto --posture
  hold` keeps the whole army at home while the danger lasts.
- **Turrets cover production and extractors, not only the HQ.** A naval yard one
  row outside the cannon line is the first thing a fleet kills. After placing
  turrets, check with `steel map --around <x,y>` that the yards and extractors sit
  inside their reach.
- **Match the turret to the threat.** MG turrets stop light units and nothing
  else; cannon turrets (then Bastions, range 7.5) stop tanks and ships; AA turrets
  and SAM sites stop aircraft and warheads. A front of MG turrets falls to a
  handful of tanks.
- **Keep one production line out of reach.** Anything within range of the enemy's
  guns, including missile subs off the coast (range 13), can be taken in one
  push. A second factory, yard or airbase deep behind the HQ keeps you producing
  after the front falls.
- **Guard the HQ above all.** Every factory, yard and airbase takes one of the
  production slots a headquarters opens: with it gone, none can be placed (`no
  free production slot`). See `recovery.md` if it falls.
- **Wait short while under threat.** `steel wait 30 --for lost,attack,nuke`
  during a fight; long waits are for quiet stretches.

## Reading an attack

`steel enemies` during an attack tells you what to build next: `counters.md` has
the answer for each unit. If the attackers are something your army cannot hit
(aircraft over tanks, submarines under surface guns), no amount of that army
helps; `--favour` the answer at once. Mark the attacker that does the most harm
(`steel priority <id>`) so every gun that reaches it fires on it first.
