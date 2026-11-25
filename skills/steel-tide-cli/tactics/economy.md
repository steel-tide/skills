# Economy

## Income

- **Extractor upgrades are the best money in the game** once the base is safe.
  An extractor mines 1.4 metal a second, an Advanced one 3.0, a Deep-Core 8.0:
  one Deep-Core is nearly six plain extractors on a single deposit. `steel
  upgrade extractor --when-affordable` keeps raising them for the rest of the
  match, the cheapest step first, as the metal comes (`steel spend` shows it;
  `--def extractor` stops at Advanced). By hand, `steel upgrade extractor`
  starts every one it can pay for now and says which it left. A standing
  upgrade waits while a factory with standing production stands idle and
  while the grid is short of power, so the army and the lights come first;
  `steel upgrade <ids> -w` buys those ahead of the factories. Before the
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

- **Put the building on the spend list.** Standing production spends as it
  earns, so a naval yard, a T2 factory, a radar or a silo waits for a moment
  the refills happen to leave the metal lying. `steel build <it> --near <x,y>
  -w` (or `upgrade <ids> -w`, `research <node> -w`, `nuke queue <silo> -w`)
  saves for it: the refills leave its price alone and it goes the second it
  can. A building starts at three quarters of its price; the refusal gives
  the figures, and says what the committed metal is promised to.
- **Committed is not free.** Every unit on a queue holds its price, so a
  queue stacked by hand (`produce factory mbt -n 6`) is 1,000 metal nobody
  can spend on a building. Standing orders keep one unit in work at a time
  for that reason; a second factory is throughput, a deep queue is not.
- **The reserve is a floor.** `steel produce reserve <metal>` keeps that much
  from every refill, for what you will ask for by hand next. `steel produce
  stop` stops the units altogether.
- **Shape what the lines make.** The standing orders on a factory are its
  mix, taking turns by share: `steel produce factory mbt -n 2 --repeat`, `…
  arty --repeat`, `… flak --repeat` makes two tanks to each gun and each
  flak. `steel produce stop <factory>` and set them again when `steel enemies`
  changes. One factory feeds one standing set; a second factory doubles the
  army.
- **Check what you laid.** A hand `build` is a blueprint an engineer walks to;
  `steel buildings --def <def>` shows it standing or still a site. Idle
  engineers walk to nearby sites by themselves.
- **Your own units can block a build.** `something is in the way` on a deposit
  can mean units parked on it; move them a few tiles off.
