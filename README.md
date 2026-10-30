# Steel Tide skills

Skills that teach an AI coding agent to work with [Steel Tide](https://steelti.de),
the real-time strategy game of large unit battles.

| Skill | What it teaches |
| --- | --- |
| [`steel-tide-cli`](skills/steel-tide-cli/SKILL.md) | Playing a match through the `steel` command line: joining a server, reading the battlefield, orders by hand, the standing orders the session keeps between turns (tactics, production kept running, rally groups), the game's own AI brain on call, and scripts the session runs every fraction of a second. Its `tactics/` chapters are a guide to winning: the opening, economy, counters, defense, attack, naval war, warheads, recovery, and writing scripts. |
| [`steel-tide-mods-guideline`](skills/steel-tide-mods-guideline/SKILL.md) | Making and testing a mod: new units, buildings and upgrade levels in one `mod.json`, checked with the game's own validator. |
| [`steel-tide-world-guideline`](skills/steel-tide-world-guideline/SKILL.md) | Writing a world (one `.steel-tide-world` file): maps, game modes with their players, rules and trigger program, campaigns, and their text in every language, checked with the game's own validator. Ends with a complete world to start from. |

## Install

With [skills.sh](https://skills.sh), for any agent it supports:

```bash
npx skills add steel-tide/skills --skill steel-tide-cli
npx skills add steel-tide/skills --skill steel-tide-mods-guideline
npx skills add steel-tide/skills --skill steel-tide-world-guideline
```

or all of them at once with `npx skills add steel-tide/skills`.

The playing skill needs the `steel` command line on the path:

```bash
curl -fsSL https://steelti.de/cli.sh | sh
```

Any other agent that reads `SKILL.md` folders (the
[Agent Skills](https://agentskills.io) layout) can take a folder from `skills/`
and put it wherever it loads skills from.

## License

MIT
