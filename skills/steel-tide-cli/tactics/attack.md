# Attacking

- **Mass, then go.** One force at 16 to 20 units with helis alongside takes an
  enemy base in minutes; the same units sent in fours are killed in fours.
  `steel force main --size 16 attack <x,y>` gathers first and launches as one
  body.
- **Name the target.** `steel force main attack #<id>` goes for that building;
  `attack auto` chases the nearest known enemy, often a stray unit. Get ids from
  `steel enemies`.
- **Production first, then the HQ.** Kill factories, yards and airbases before
  anything else: a faction without production cannot replace what it loses. Then
  the HQ, which usually ends it.
- **Break turret lines from range.** Howitzers outrange turrets; let them shell
  the line while the tanks wait just outside it, then go in.
- **Air goes with the army.** Helis flying ahead of the tanks meet every AA gun
  alone. In the same force, or in a second force sent at the same target, they
  arrive together.
- **Scout before a naval strike.** Ships sent at a coast they cannot see die to
  whatever waits there, and a lost fleet is a coast you cannot defend. A drone
  over the water first; send the fleet only with a clear edge.
- **Several enemies: take them one at a time.** Put everything into the weaker or
  nearer one, finish it, take its ore, then turn. Two half attacks win nothing.
- **Hold what you took.** After a base falls, `expand` its deposits and leave a
  turret; otherwise the enemy walks back in.
- **Raids pay on soft targets.** `steel force air --size 4 harass` against lone
  extractors on islets or at the map's edge costs the enemy income for little.
  Pull it back the moment it meets guns it cannot beat.
