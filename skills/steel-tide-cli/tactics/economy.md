# Economy and the plan

## Income

- **Extractor upgrades are the best money in the game** once the base is safe:
  `keep extractor2 N`, then `keep extractor3 N`. An extractor mines 1.4 metal a
  second, an Advanced one 3.0, a Deep-Core 8.0: one Deep-Core is nearly six plain
  extractors on a single deposit. Before the base is safe they are what gets you
  killed (see `opening.md`).
- **Claim ore by name.** `steel expand <id>` sends the right builder: an engineer
  on the same ground, an engineer boat across water. A bare `keep extractor N`
  larger than your side of the map holds sends engineers to lay blueprints deep in
  enemy land, where they die.
- **An upgrade goal only upgrades.** `keep extractor3 4` never rebuilds an
  extractor that was destroyed. Pair it with `keep extractor 4`, or `expand`
  again.
- **Take a beaten enemy's ore at once.** When a faction is eliminated, `expand`
  its deposits; your economy should grow while the survivors' does not.

## Contested deposits

A deposit between two bases changes hands all game. Holding one takes a guard:
a cannon turret on it (`build cannonturret --near <x,y>`) and part of the army
posted there (`force <name> defend <x,y>`). Every extractor the enemy rebuilds
on a deposit you watch, without an escort, is an engineer to kill.

## Making the plan spend where you mean

- **An expensive goal must `--save`.** Without it, cheap goals and production
  take the metal first and the naval yard, the T2 factory or the silo waits
  forever: `keep navyard 1 --save`.
- **Goals come before production.** A production line reads idle while goals are
  held for metal. When the army matters more than the next building, remove the
  goal (`keep <building> 0`) or move it later (`--before`, `--then`). `steel plan`
  shows what each goal holds and why.
- **Pause a line to bank for something.** `steel mix navyard --clear` stops a
  line; set the mix again to resume. Useful before a silo, an HQ rebuild or a
  big upgrade.
- **Cap what can clog the unit cap.** `mix factory arty=1.5:8` stops at eight
  howitzers; a line with no cap fills the cap with one kind.
- **Check what a pinned goal built.** A goal with `--at x,y` may count buildings
  of that kind standing anywhere and report `done` with nothing at the spot. Look
  with `steel buildings --def <def>`; when it did not place, `steel build <def>
  --near x,y` does.
- **Your own units can block a build.** `something is in the way` on a deposit
  can mean your force is parked on it; move the force's post a few tiles off.
