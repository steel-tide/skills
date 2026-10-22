# Scripts: what must happen faster than your turns

A turn of yours takes tens of seconds of match time; a fight turns in one. The
brain covers the gap with its own rules. A script covers it with yours. Write a
script for anything that has to be answered within seconds and will come up
again: a unit that must not stand where it is, a group under fire it cannot see,
a line that must not stand idle, a department you would rather run your way.

## The loop

1. Write the file. `steel script types > steel.d.ts` beside it gives your
   editor the API; `/// <reference path="./steel.d.ts" />` at the top.
2. `steel script try <file>`: one run against the match now. It prints every
   order it would give, or why each would be refused, and sends nothing.
3. Before a live match, `steel lab --vs veteran --script <file> --seeds 5`:
   headless, a match a second. Compare with and without the script over the
   same seeds; one seed proves nothing.
4. `steel script run <file>`. Then `steel script` for its runs, orders,
   refusals and errors, and `steel script log <name>` for what it said. An
   error or a refusal also shows in `steel wait`.
5. Change the file and run it again under the same name to replace it.

## Patterns

**Keep a battery out of reach.** Artillery outranges what it shoots and dies to
whatever reaches it.

```ts
rules: {
  arty(u, s) {
    const close = s.enemies({ near: u, within: 6, armed: true, seen: 0 });
    if (close.length) s.move(u, s.away(u, s.nearest(u, close)!, 5));
  },
},
```

**Get out from under a gun you cannot see.** Units dying with nothing in sight
are being shelled from beyond their sight. The loss names the gun; pull back
past its reach (13 tiles covers a rocket launcher) away from where the enemy
is, and send something that sees far (a drone, a jet) to find it.

```ts
tick(s) {
  const shelled = s.events.filter((e) => e.kind === 'lost' && e.data?.killers &&
    Object.keys(e.data.killers as object).some((k) => ['arty', 'mlrs', 'scorpion'].includes(k)));
  if (!shelled.length) return;
  const enemy = s.nearest(s.base, s.enemies({ building: true })) ?? s.spawns.find((p) => !p.own)!;
  for (const u of s.units({ armed: true, domain: 'ground' })) {
    if (s.enemies({ near: u, within: u.vision, seen: 0 }).length) continue;   // it can see what it fights
    if (s.dist(u, enemy) < s.dist(s.base, enemy)) s.move(u, s.toward(u, s.base, 8));
  }
  const eye = s.units({ def: ['drone', 'jet'], idle: true })[0];
  if (eye) s.move(eye, s.toward(s.base, enemy, s.dist(s.base, enemy) * 0.6));
},
```

**Focus fire.** A group spreading its shots kills nothing; the same shots on
the weakest thing in reach kill one at a time.

```ts
rules: {
  army(u, s) {
    const inReach = s.enemies({ near: u, within: u.range, seen: 0 });
    if (!inReach.length) return;
    const weakest = inReach.reduce((a, b) => (a.hp < b.hp ? a : b));
    if (u.target !== weakest.id) s.attack(u, weakest);
  },
},
```

**Take a department.** The brain runs the rest; the script runs one of them its
own way. Hand the department over once, keep state in `s.memory`.

```ts
tick(s) {
  if (!s.memory.mine) { s.steer({ run: ['economy', 'research', 'production', 'nukes'] }); s.memory.mine = true; }
  const army = s.units({ armed: true, domain: 'ground' });
  const idle = army.filter((u) => u.idle);
  const target = s.nearest(s.base, s.enemies({ building: true }));
  if (target && army.length >= 12 && idle.length) s.attackMove(idle, target);
},
```

## What to know

- A script sees what your seat sees and nothing more: an enemy out of sight is
  a building remembered (`seen` > 0) or nothing at all.
- A unit a script orders is out of the brain's hands until `s.attach(u)`. A
  rule that orders every unit every run takes the whole army from the brain.
- An order to no units is an error; five failed runs in a row stop the script.
  Guard every list: `if (idle.length) ...`.
- The same order to the same units is sent once in three seconds, and all
  scripts together send 30 orders a sim second. Order what changed, not
  everything every run.
- `s.holdFire(u)` keeps a unit from firing on its own (an ambush, a column
  slipping past guns); `s.holdFire(u, false)` or any attack order releases it,
  and `u.holdFire` says which it is on. Like any order, it takes the unit from
  the brain.
- Several orders in one run are each checked against the same metal: the
  second may be refused for metal the first took. Spend against `s.free`, and
  count down as you go.
- A run has 100 ms; a loop that does not end is cut off, and the script starts
  over without its memory.
