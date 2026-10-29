# Counters: turning `steel enemies` into production

Read `steel enemies` every few minutes. When what they field changes, change
the standing orders to answer it (`steel produce stop <factory>`, then `steel
produce <factory> <unit> -n N --repeat` for the answer). `steel defs <unit>` gives a unit's weapons,
range and what it is strong and weak against; this table is what battles bore out.

| Their unit              | Answer                                                                 | Wasted on it                              |
| ----------------------- | ---------------------------------------------------------------------- | ----------------------------------------- |
| tanks (Wolf, Bison)     | Cobra helis standing off at 5.5 (past a Wolf's 4 and a Bison's roof gun at 4.5), Bisons, Vipers, cannon turrets | flak (its gun only scratches armour) |
| Cobra helis             | flak (6.5, past their 5.5), SAM sites, fighters, Frigates; Wolves and Bisons up close | tanks left to chase them |
| Destroyer (gun 6.2)     | howitzers (range 11), Bastion cannons (7.5), helis (it has no AA)       | gunboats fed in one at a time             |
| Frigate (SAM 8.5)       | other ships, howitzers                                                 | helis                                     |
| Kraken missile sub      | anything with a torpedo: Destroyer, Barracuda sub (it cannot shoot ships at all) | T1 boats (cannot hit a sub), turrets |
| bombers, strike jets    | SAM sites and fighters over whatever they would hit                     | nothing, if the target stands alone        |
| turret lines            | howitzers from beyond their reach, bombers                              | a trickle of tanks into the guns           |

`produce` takes a unit's id or its name: `ltank` or `wolf`, `mbt` or
`bison`, `td` or `viper`, `heli` or `cobra`, `flak`, `sam`, `arty`, `mlrs`,
`destroyer`, `frigate`, `sub` or `barracuda`, `kraken`, `fighter`, `jet`,
`bomber`; `steel defs <factory>` says which a building makes at each level.
Turrets are laid (`steel build aaturret --near <x,y>`).

## Rules that hold

- **Build for what you see, not a fixed mix.** A line that keeps making flak
  against an enemy with no aircraft is metal thrown away; take it off the
  standing orders until aircraft show up, and put it back the turn they do.
- **Range wins sieges.** The howitzer outranges every ship gun and every turret.
  A line of them behind your shore kills Destroyers that cannot reach back; a line
  of them outside the enemy's turrets takes the turrets for free.
- **Helis are the best tank killers and the most fragile unit you own.** They
  die to fighters, to SAMs on ships and to a column's machine guns and light
  cannon if they close in. Send them with the army, never ahead of it, and keep
  them out of raids on a defended base.
- **Aircraft carry a load and go home for more.** A dry aircraft flies back to
  an airbase by itself (not on hold, not mid-move), rearms within four tiles of
  it and returns to its order: keep airbases near the front, and hit the enemy's
  aircraft on the way home.
- **Mixed forces beat pure ones.** Tanks to hold the line, tank destroyers
  against armour, a few flak or a SAM launcher against aircraft, howitzers
  behind. One kind alone on the standing orders is an army with one answer.
- **Shoot the dangerous one first.** `steel priority <id>` on the enemy unit
  that matters most (the MLRS, the SAM launcher under your helis, the Frigate)
  turns every gun that can reach it onto it.
- **Promotion matters.** Units that survive fights rank up (Veteran, Elite) and
  hit harder. An assault falls back by itself when half its worth is gone;
  `steel tactic retreat <group> --below 40` sends each unit home before it
  dies and lets it go once mended.
