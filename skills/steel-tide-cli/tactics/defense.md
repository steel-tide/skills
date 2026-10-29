# Defending the base

- **Guard from the whistle.** `steel tactic guard` at the start of every match:
  anything that comes within 24 tiles of the base is met by every armed unit,
  and they go back where they stood once it is gone. Without it, a unit stands
  and watches the base burn until something comes in its own gun's reach.
  `steel tactic retreat --all --below 30` beside it keeps the hurt ones alive
  to be repaired. A unit in an assault is not in the guard: keep a second group
  home, or call the assault back (`attack.md`).
- **Read the `!` lines and `steel threats`, not the events.** When a reply
  opens with `! 06:02 under attack at 14,8 (factory#17)`, run `steel threats`:
  it says what the group is, what it is worth, how fast it closes and when it
  arrives, what it has under its guns, and whether what you have in reach
  holds or is outgunned. Outgunned: pull the exposed units back behind the
  turrets (`steel move <units> @hq`), put the counter on the standing orders
  (`steel produce factory <unit> -n 2 --repeat`), and let the turrets and HQ
  take the first blow. Holds: let the guard fight, and add `steel tactic focus
  --all` so the shots land on one thing at a time.
- **Fire from the fog.** A loss line ending `— out of sight` is artillery
  or a turret you cannot see; the line says how far to pull back. Pull back
  that far, then get eyes on it (a radar, a drone) and `steel priority` it.

- **Be where the attack will land before it lands.** The guard meets what comes
  inside its radius, but only once it is seen. When you know the way an attack
  will come, post a group there (`steel tactic guard <group> --at <x,y>
  --radius 12`) and put turrets on it. Moving there when the `attack` event
  fires is a minute late, and a minute decides it.
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
helps; put the answer on the standing orders at once. Mark the attacker that does the most harm
(`steel priority <id>`) so every gun that reaches it fires on it first.
