# Defending the base

- **Post defenders before the attack, not after.** `steel force main defend
  <x,y> --radius 15` where the attack will land. Moving a force there when the
  `attack` event fires is a minute late, and a minute decides it.
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
- **Guard the HQ above all.** Production buildings need a standing HQ: with it
  gone, no new factory, yard or airbase can be placed ("a headquarters pays for
  three lines"). See `recovery.md` if it falls.
- **Wait short while under threat.** `steel wait 30 --for lost,attack,nuke`
  during a fight; long waits are for quiet stretches.

## Reading an attack

`steel enemies` during an attack tells you what to build next: `counters.md` has
the answer for each unit. If the attackers are something your army cannot hit
(aircraft over tanks, submarines under surface guns), no amount of that army
helps; switch the lines at once.
