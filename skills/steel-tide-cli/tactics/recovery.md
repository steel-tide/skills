# When it goes wrong

## The army is gone and the enemy is in the base

- **Spend on what their army cannot hit.** Tanks cannot shoot up: every metal
  into Cobra helis. Missile subs cannot shoot ships: torpedoes. One unit their
  army cannot touch is worth ten it can.
- **Drop every goal that is not a turret or a unit.** `steel keep --clear`, then
  only the turrets you need: an upgrade or a new extractor now is metal the
  enemy will destroy.
- **Pull engineers back and keep them repairing.** They rebuild the base
  afterwards; lost now, nothing rebuilds.

## The HQ is gone

Production buildings need a standing HQ. Without one, no new factory, yard or
airbase can be placed ("a headquarters pays for three lines"), though what still
stands keeps producing.

- **Rebuild at once, out of the enemy's reach**: `steel keep hq 1 --at <x,y>
  --first --save`. It costs 2,500 and needs three quarters of that on hand to
  start, so stop every line (`steel mix <line> --clear`) until it is placed.
- **Keep engineers alive until it stands.** Without the HQ, no new engineers
  come.

## A warhead landed

1. Read the losses (`steel events -n 40`): which SAM sites, yards and power died.
2. Power first (the autopilot does this), then SAM sites over what is left, then
   production.
3. Ask why it got through: which side had fewer SAM sites? Cover it before the
   next one (see `warheads.md`).

## The economy is falling behind

If `steel brief` shows more enemy extractors than yours and your army cannot
take theirs, raid them: helis or a small force on `harass`, or artillery at a
contested deposit. Income you deny counts the same as income you earn.
