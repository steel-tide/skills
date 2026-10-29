# When it goes wrong

## The army is gone and the enemy is in the base

- **Spend on what their army cannot hit.** Tanks cannot shoot up: every metal
  into Cobra helis (`steel produce stop`, then `steel produce airbase heli -n 2
  --repeat`). Missile subs cannot shoot ships: torpedoes. One unit their army
  cannot touch is worth ten it can.
- **Stop spending on what the enemy will destroy.** No upgrades, nothing to
  the War Lab, only the turrets you need: an upgrade or a new extractor now is
  metal the enemy will take.
- **Pull engineers back and keep them repairing.** `steel move --engineers
  <x,y>`, then `steel repair <engineers> <building>`. They rebuild the base
  afterwards; lost now, nothing rebuilds.

## The HQ is gone

Every factory, yard and airbase takes one of the production slots a
headquarters opens. Without one, none can be placed (`no free production slot`),
though what still stands keeps producing.

- **Rebuild at once, out of the enemy's reach.** `steel produce reserve
  2500`, then `steel build hq --near <x,y>` somewhere their guns do not reach.
  It costs 2,500 and needs three quarters of that on hand to start; the
  reserve keeps the factories from spending it first. `steel produce reserve
  0` once it is laid.
- **Keep engineers alive until it stands.** Without the HQ, no new engineers
  come.

## A warhead landed

1. Read the losses (`steel events -n 40`): which SAM sites, yards and power died.
2. Power first, then SAM sites over what is left, then production.
3. Ask why it got through: which side had fewer SAM sites? Cover it before the
   next one (see `warheads.md`).

## The economy is falling behind

If `steel brief` shows more enemy extractors than yours and your army cannot
take theirs, raid them: a few helis sent by hand, or a script, at their outlying
extractors; artillery at a contested deposit; or the whole army sent at their
ore (`steel tactic assault army --to <their extractors>`). Income you deny
counts the same as income you earn.
