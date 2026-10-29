# Across water

When `steel brief` says `reach  the enemy is across water`, ground armies stay
home unless something carries them. What reaches the enemy: ships, aircraft, and
ground units landed by transport (`steel pickup`, `steel unload`).

## Build

- **A naval yard and an airbase early**, before the second factory, with
  engineer boats for the islets; a yard needs a reserve first (`economy.md`).
  An engineer boat claims an islet: `steel build extractor --deposit <id> --by
  <boat id>`.
- **Turn the factory down.** Ground units at home are a garrison: one tank
  on the factory's standing order, and the rest of the metal into ships and
  aircraft (`steel produce navyyard destroyer -n 2 --repeat`).
- **The T2 yard is the real fleet.** Destroyers (torpedoes, 1,300 hp) and
  Frigates (a SAM) are what hold the water; T1 gunboats die one by one to them.
- **A second yard away from the enemy's side of the coast.** A missile sub
  shelling from 13 tiles, or one warhead, can take every yard on one shore.

## Fight

- **Artillery on the shore.** Howitzers (range 11) behind your coast outrange
  every ship gun; cannon turrets upgraded to Bastions (7.5) outrange a
  Destroyer's 6.2.
- **Keep the fleet under your guns until it is strong.** A small fleet posted
  just offshore (`steel move --sea --armed <x,y>`, then `steel hold --sea
  --armed`) fights with the turrets behind it. Out alone, it is picked off.
- **Answer submarines with torpedoes.** A missile sub cannot hit ships, only
  land, so one Destroyer or Barracuda kills it for free (put them on the
  yard's standing order). Nothing else you have touches it.
- **Scout the water before a strike.** A fleet sent blind at an enemy coast dies
  to what it cannot see, and it was your coast's only answer to their ships.
- **Aircraft cross everything.** Helis and strike jets raid islet extractors;
  bombers level buildings. Enemy fighters and SAMs kill them, so raid where those
  are not.

## The islets

Ore between the coasts is the prize. Claim it early with engineer boats, and
expect the enemy to land a few tanks by lift or helicopter to take it back. A
cannon turret beside each islet extractor, or helis posted over them, keeps it.
