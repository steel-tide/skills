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

`steel` also carries its own copy of that skill: `steel skill --install` writes it
into `./.claude/skills` without this repository.

Any other agent that reads `SKILL.md` folders (the
[Agent Skills](https://agentskills.io) layout) can take a folder from `skills/`
and put it wherever it loads skills from.

## Layout

```
skills/steel-tide-cli/
├── SKILL.md            the verbs, the command loop, and a table routing to a chapter
└── tactics/            one chapter per part of the game, loaded when it applies
    ├── opening.md
    ├── economy.md
    ├── counters.md
    ├── defense.md
    ├── attack.md
    ├── naval.md
    ├── warheads.md
    └── recovery.md
skills/steel-tide-mods-guideline/
└── SKILL.md            the mod format and the registry's rules, generated from the game
```

## Where they come from

Both skills are kept with the game's source, which is not public, and copied here
when they change. `steel-tide-cli` is the same skill the `steel` binary embeds.
`steel-tide-mods-guideline` is generated from the mod format itself, together with
the registry's validator, so the brief and the checker never disagree. A pull
request here would be overwritten by the next copy; to suggest a change, open an
issue.

## License

MIT
