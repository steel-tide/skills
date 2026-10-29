# Economy

## Income

- **Extractor upgrades are the best money in the game** once the base is safe.
  An extractor mines 1.4 metal a second, an Advanced one 3.0, a Deep-Core 8.0:
  one Deep-Core is nearly six plain extractors on a single deposit. `steel
  upgrade <id>` one at a time as the metal comes, the nearest to the HQ first
  (`steel upgrade extractor` takes every plain one it can pay for). Before the
  base is safe they are what gets you killed.
- **Claim ore by name.** `steel build extractor --deposit <id>` sends the
  nearest free engineer. `steel deposits` says who can reach each deposit
  (`reach`: land, boat, air); across water, name an engineer boat with
  `--by <id>`. A deposit deep in enemy land costs the engineer you send.
- **Take a beaten enemy's ore at once.** When a faction is eliminated, claim
  its deposits; your economy should grow while the survivors' does not.

## Contested deposits

A deposit between two bases changes hands all game. Holding one takes a guard:
a cannon turret on it (`steel build cannonturret --near <x,y>`) and a few units
posted there (`steel group post <ids>`, `steel tactic guard post --at <x,y>
--radius 12`). Every extractor the enemy rebuilds on a deposit you watch,
without an escort, is an engineer to kill.

## Money for buildings while the factories run

- **The reserve is the building fund.** Standing production spends as it
  earns, so a naval yard, a T2 factory, a radar or a silo waits for a moment
  the refills happen to leave the metal lying. `steel produce reserve
  <price>` holds that much back from every refill; lay the building; lower
  the reserve. A building starts at three quarters of its price; the refusal
  gives the figures.
- **Bank for something.** The same reserve banks for a silo, an HQ rebuild or
  a big upgrade. `steel produce stop` stops the units altogether.
- **Shape what the lines make.** The standing orders are the mix: `steel
  produce factory mbt -n 2 --repeat`, `… arty -n 1 --repeat`, `… flak -n 1
  --repeat`; `steel produce stop <factory>` and set them again when `steel
  enemies` changes. One factory feeds one standing set; a second factory
  doubles the army.
- **Check what you laid.** A hand `build` is a blueprint an engineer walks to;
  `steel buildings --def <def>` shows it standing or still a site. Idle
  engineers walk to nearby sites by themselves.
- **Your own units can block a build.** `something is in the way` on a deposit
  can mean units parked on it; move them a few tiles off.
