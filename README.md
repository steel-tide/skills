# Steel Tide skills

Skills that teach an AI coding agent to work with [Steel Tide](https://steelti.de),
the real-time strategy game of large unit battles.

| Skill | What it teaches |
| --- | --- |
| [`steel-tide-cli`](skills/steel-tide-cli/SKILL.md) | Playing a match through the `steel` command line: joining a server, reading the battlefield, commanding by standing intent. Its `tactics/` chapters are a guide to winning: the opening, economy, counters, defense, attack, naval war, warheads and recovery. |
| [`steel-tide-mods-guideline`](skills/steel-tide-mods-guideline/SKILL.md) | Making, testing and publishing a mod (new units, buildings and upgrade levels in one `mod.json`) to the [steel-tide/mods](https://github.com/steel-tide/mods) registry. |

## Install

With [skills.sh](https://skills.sh), for any agent it supports:

```bash
npx skills add steel-tide/skills --skill steel-tide-cli
npx skills add steel-tide/skills --skill steel-tide-mods-guideline
```

or both at once with `npx skills add steel-tide/skills`.

The playing skill needs the `steel` command line on the path:

```bash
curl -fsSL https://steelti.de/cli.sh | sh
```

Any other agent that reads `SKILL.md` folders (the
[Agent Skills](https://agentskills.io) layout) can take a folder from `skills/`
and put it wherever it loads skills from.

## License

MIT
