# Economy

## Income

- **Extractor upgrades are the best money in the game** once the base is safe.
  An extractor mines 1.4 metal a second, an Advanced one 3.0, a Deep-Core 8.0:
  one Deep-Core is nearly six plain extractors on a single deposit. The brain
  climbs them itself while it runs `research`; to hurry one, `steel buildings
  --def extractor` and `steel upgrade <ids>`. Before the base is safe they are
  what gets you killed.
- **Claim ore by name.** `steel build extractor --deposit <id>` sends the
  nearest free engineer. `steel deposits` says who can reach each deposit
  (`reach`: land, boat, air); across water, name an engineer boat with
  `--by <id>`. A deposit deep in enemy land costs the engineer you send.
- **Take a beaten enemy's ore at once.** When a faction is eliminated, claim
  its deposits; your economy should grow while the survivors' does not.

## Contested deposits

A deposit between two bases changes hands all game. Holding one takes a guard:
a cannon turret on it (`steel build cannonturret --near <x,y>`) and a few units
posted there by hand (`steel move <units> <x,y> --attack`, then `steel hold
<units>`; they stay yours until `steel attach`). Every extractor the enemy rebuilds on a
deposit you watch, without an escort, is an engineer to kill.

## Making the brain spend where you mean

- **Something dear by hand needs a reserve.** The brain spends as it earns, so
  a naval yard, a T2 factory or a silo you lay yourself waits for a moment it
  happens to leave the metal lying. `steel auto --reserve <price>`, lay it,
  then `--reserve 0`. A building starts at three quarters of its price; the
  refusal gives the figures.
- **Bank for something.** The same reserve holds metal back from everything
  the brain does: before a silo, an HQ rebuild or a big upgrade. Leaving
  `production` out of `--run` stops its units altogether.
- **Shape what the lines make.** `steel auto --favour arty=0.5,flak=0` makes
  howitzers half as often and flak never; `--favour clear` puts it back. A unit
  favoured above 1 is always built at least a little.
- **Check what you laid.** A hand `build` is a blueprint an engineer walks to;
  `steel buildings --def <def>` shows it standing or still a site. The brain
  crews your blueprints and counts them toward its plans.
- **Your own units can block a build.** `something is in the way` on a deposit
  can mean units parked on it; move them a few tiles off.
