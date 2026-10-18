# Warheads

A nuclear warhead costs 2,500 metal and three minutes. Its blast does up to
3,000 damage within 6 tiles: enough for any ordinary building, while a
headquarters (4,000) survives one. It flies as an aircraft. Only **SAM sites**
(upgraded AA turrets) always fire at it; other anti-air only at rank 2 or above;
the Missile Interrupter never does.

## Defending against them

- **Start when you first see a radar, reactor or silo** in `steel enemies`, not
  after the first launch. The first warhead can land before `wait --for nuke`
  wakes you.
- **Three SAM sites over every cluster of value.** The HQ, the production yards,
  the silo. Three SAM sites around an HQ can shoot down warhead after warhead;
  two over a yard can let one through, and one blast there takes every yard, the
  airbase and the reactor at once.
- **Cover everything, because the enemy aims at the gap.** A launcher whose
  warheads keep dying will aim at the part of your base with the fewest SAM sites.
- **Put them where the flight passes.** A warhead flies a straight line from the
  silo. The launch report names the buildings under its line; SAM sites on that
  side of each cluster get the shot.
- **Rebuild SAM sites first after any attack.** Missile subs and bombers kill
  them like any building; a base whose SAM sites fell is open to the next
  warhead.
- `steel keep samsite N` raises AA turrets and upgrades them. When a goal cannot
  place one where you need it, `steel build aaturret --near <x,y>` and then
  `steel upgrade <id>`.

## Your own

- `steel keep warhead 1` builds the radar, reactor and silo it needs and saves
  up. Guard the silo with AA; a single bomber can kill an unguarded one.
- `steel nuke targets` lists the best points to aim at: seldom a building's
  centre, usually the ground between several dear ones that one blast kills
  together. Each row says what it would destroy and only damage, the chance the
  warhead gets past the SAM sites you know of from your silo, and what of yours
  it would take. Above a score of about 1,600 it is worth the warhead. Two SAM
  sites on the way let a warhead through; three stop it more often than not,
  and four or more nearly always.
- **Aim off the target, away from their SAM sites.** The blast reaches 6 tiles,
  so a point 3 tiles from the target, on the far side from their SAM cluster, still
  catches it while the flight line stays out of the SAM sites' reach.
- `steel wait --for nuke` wakes on the impact and on the strike report after it;
  fog hides what is not in sight, so send a drone to see.
- **Weigh it against an army.** 2,500 metal is nine battle tanks. Against a
  well-covered enemy, the army usually does more.
