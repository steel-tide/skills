# When it goes wrong

## The army is gone and the enemy is in the base

- **Spend on what their army cannot hit.** Tanks cannot shoot up: every metal
  into Cobra helis (`--favour heli=5`). Missile subs cannot shoot ships:
  torpedoes. One unit their army cannot touch is worth ten it can.
- **Stop spending on what the enemy will destroy.** Leave `research` out of
  `--run` (no upgrades, nothing to the War Lab) and lay only the turrets you
  need, by hand: an upgrade or a new extractor now is metal the enemy will take.
  Hand it back once the base holds.
- **Pull engineers back and keep them repairing.** `steel move --engineers
  <x,y>`, then `steel repair <engineers> <building>`. They rebuild the base
  afterwards; lost now, nothing rebuilds.

## The HQ is gone

Every factory, yard and airbase takes one of the production slots a
headquarters opens. Without one, none can be placed (`no free production slot`),
though what still stands keeps producing.

- **Rebuild at once, out of the enemy's reach.** The brain lays one again by
  itself, near the old base. Where that is under the enemy's guns, choose the
  place: `steel auto --reserve 2500`, then `steel build hq --near <x,y>`. It
  costs 2,500 and needs three quarters of that on hand to start; the reserve
  keeps the brain from spending it first. `--reserve 0` once it is laid.
- **Keep engineers alive until it stands.** Without the HQ, no new engineers
  come.

## A warhead landed

1. Read the losses (`steel events -n 40`): which SAM sites, yards and power died.
2. Power first (the brain's economy does this), then SAM sites over what is
   left, then production.
3. Ask why it got through: which side had fewer SAM sites? Cover it before the
   next one (see `warheads.md`).

## The economy is falling behind

If `steel brief` shows more enemy extractors than yours and your army cannot
take theirs, raid them: a few helis sent by hand, or a script, at their outlying
extractors; artillery at a contested deposit; or the brain's whole offensive
`--target`ed at their ore. Income you deny counts the same as income you earn.
