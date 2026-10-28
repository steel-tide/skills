---
name: steel-tide-world-guideline
description: Make a world for Steel Tide, the browser RTS — maps, game modes with a trigger program, campaigns and their text in one .steel-tide-world JSON file. Use when asked to create or edit a Steel Tide world, scenario, custom game mode or campaign, write its triggers, or check the file.
license: MIT
---
# Making a Steel Tide world: a brief for a coding agent

You are helping make a world for Steel Tide (https://steelti.de), a browser real-time strategy game.
A world is one JSON file (`*.steel-tide-world`) that carries maps, **game modes** (the format calls them scenarios: who plays, how the match opens, how it ends, and a trigger program that runs it), campaigns that chain game modes into missions, unit packs, its own text in every language, and images and sounds. The game's World Editor reads and writes exactly this file, so anything written here can be opened and finished there.
Read this whole brief once, start from the example at the end, and check the file after every change (see Check). When in doubt, prefer the smallest world that plays.

## The file

| field | type | what it is |
| --- | --- | --- |
| `format` | "steel-tide-world" | always this |
| `v` | 1 | the format version |
| `id` | id | the world's stable identity: letters, digits and `_ . : -`, up to 120 characters; never change it after sharing the world |
| `name`, `version` | string | its name (up to 160 characters) and its content version, e.g. "1.0.0" |
| `author`, `description`, `license` | string? | shown in the world library |
| `cover` | string? | a path in `assets`, the library's picture of the world |
| `maps` | { id: Map } | the battlefields (at most 128) |
| `scenarios` | GameMode[] | the game modes (at most 128) |
| `campaigns` | Campaign[] | missions in order (at most 64); `[]` for none |
| `mods` | DefinitionPack[] | unit packs, each a mod manifest (see the `steel-tide-mods-guideline` skill); `[]` for none |
| `texts` | { key: [en, zh, ko] } | every line a player reads, in English, Chinese and Korean |
| `assets` | { path: data URL }? | images and sounds embedded in the file |

The file may be 16 MB (128 MB when it carries large unit packs). Every id is letters, digits and `_ . : -`.

## Coordinates

A tile is 32 world pixels. **Placed units (`instances`, `spawn`) are in world pixels**, at their centre; everything else — regions, routes, `wave`, `order`, `ping` and `sound` — is in **tiles**. x runs east, y south, from the top-left corner. A player is a **seat**: its index in `cfg.players`, from 0, and every `owner` is a seat index.

## Maps

A map is the game's own map file, the same JSON the map editor saves as `.steel-tide-map`. The surest way to a good map is to paint it in the game (Editor → Maps, then export) and paste the file's JSON into `maps.<id>`. To write one:

| field | what it is |
| --- | --- |
| `format`, `v` | "steel-tide-map", 1 |
| `name` | the map's name |
| `w`, `h` | its size in tiles, 16–512 a side |
| `terrain` | the tile grid, `w × h` bytes row by row, run-length coded: pairs of bytes (run length 1–255, tile kind), the whole byte string in base64 |
| `spawns` | where each seat opens, tile x,y, one per seat at least (at most 8) |
| `deposits` | metal deposits, tile x,y |

Tile kinds: 0 deep water, 1 water, 2 sand, 3 grass, 4 forest, 5 mountain (impassable but to aircraft), 6 road, 7 mud, 8 marsh, 9 snow, 10 rubble, 11 lava, 12 cliff facing north, 13 cliff facing east, 14 cliff facing south, 15 cliff facing west, 16 city block, 17 shallows (wadeable, a ship runs aground), 18 ice.

A grid of all grass, 64×64, in JavaScript:

```js
const w = 64, h = 64, grid = new Uint8Array(w * h).fill(3); // 3 = grass
const bytes = []; for (let i = 0; i < grid.length;) { let run = 1; while (i + run < grid.length && grid[i + run] === grid[i] && run < 255) run++; bytes.push(run, grid[i]); i += run; }
const terrain = Buffer.from(bytes).toString("base64");
```

## A game mode

| field | what it is |
| --- | --- |
| `id`, `name` | its id and its name in the library |
| `map` | a key of `maps` |
| `cfg` | the match: `{ "mode": "sandbox", "mapKey": "custom", "seed": n, "fog": bool, "popCap": n, "startMetal": n, "players": Seat[] }`, and optionally `weather` ("clear", "rain", "snow", "random"), `tech` (research on), `buildRadius` (false lets players build anywhere), `productionSlotsPerHQ` |
| `cfg.players` | one to eight seats: `{ "isHuman": bool, "team": 0–7, "difficulty"?: "relaxed" \| "standard" \| "seasoned" \| "veteran", "scripted"?: true, "spawn"?: n }`. A human seat is a person; otherwise the computer plays it, unless `scripted`: then it has no mind of its own and does only what triggers make it do (the right seat for waves and set pieces). `spawn` picks a map spawn other than its own index |
| `resultPolicy` | "conquest": the last side with a base wins, and triggers may end it earlier. "scripted": only a `result` action ends the match |
| `defaultBases` | every seat opens with a headquarters and engineers on its spawn |
| `mapUnits` | the units and buildings painted on the map join their seats (default true) |
| `skyMinutes` | the sun's place when the match opens, minutes after midnight |
| `instances` | units and buildings placed at the start: `{ "id", "defId", "owner", "x", "y" }` in world pixels, and optionally `angle` (radians), `hp`, `locked`, `holdPos`, `holdFire`, `aggressive`, `xp`, `scriptedFire`. A trigger finds one with `{ "instance": id }` |
| `groups` | { name: instance id[] }: named sets of instances |
| `regions` | rectangles in tiles: `{ "id", "name"?, "x", "y", "w", "h", "marker"?: { "color" } }`; `marker` outlines it for the players |
| `routes` | paths in tiles: `{ "id", "name"?, "points": [{ "x", "y" }…], "attack"? }` |
| `variables` | { name: number \| string \| bool }: the program's state and its starting values |
| `objectives` | `{ "id", "textKey", "params"?: Expr[], "team"?, "visible"? }`: the objective list; `{0}` in the text takes the first param |
| `counters` | `{ "id", "textKey", "value": Expr, "team"? }`: numbers on the HUD, recomputed every tick |
| `triggers` | the program (below) |
| `actions` | { name: Action[] }: functions, run by `call` |
| `briefing` | `{ "titleKey"?, "bodyKey"?, "dateKey"?, "locationKey"?, "image"? }`: the plate read before the match |
| `roster` | { seat: def id[] }: what that seat may build and train; a seat left out has the normal roster |
| `buildRegions` | { seat: region id[] }: where that seat may build |
| `guide` | tutorial steps; leave them to the World Editor's Tutorial module |

## Text

Players never read an id. Every `textKey`, `titleKey` and `bodyKey` names an entry in the world's `texts`, a list of three strings in the order English, Chinese, Korean. Write all three; an empty one reads as the first that is not. Key them by the game mode, e.g. `hold.objective`, so two modes never share one by accident. `{0}`, `{1}`… take the `params` in order. A trigger's `name` and `description` are notes for authors, not text.

## Triggers

A trigger is `{ "id", "name"?, "description"?, "event", "condition"?, "actions", "enabled"?, "once"?, "maxRuns"? }`: when the event happens and the condition (an expression) is true, its actions run in order. `enabled: false` starts it off (a `trigger` action turns it on); `once` lets it run one time; `maxRuns` that many. Triggers run in the order they are listed, after combat, every tick of 1/30 s.

### Events

| event | when |
| --- | --- |
| `{ "type": "start" }` | once, as the match opens |
| `{ "type": "tick", "every"?: s }` | every `every` seconds of match time (every tick, 1/30 s, without it) — with a `condition`, this is how a rule is watched |
| `{ "type": "time", "at": s, "every"?: s }` | at `at` seconds of match time, then every `every` seconds if given |
| `{ "type": "destroyed", "query"?: Query }` | a unit or building matching the query is destroyed |
| `{ "type": "created", "query"?: Query }` | one appears: trained, built (as a site), spawned or placed |
| `{ "type": "completed", "query"?: Query }` | a building site matching the query is finished |
| `{ "type": "removed", "query"?: Query }` | one was taken off the field by a `remove` action |
| `{ "type": "escaped", "query"?: Query }` | one was taken off by `remove` with `"reason": "escaped"` |
| `{ "type": "enter", "region": id, "query"?: Query }` | one matching the query comes into the region |
| `{ "type": "leave", "region": id, "query"?: Query }` | one matching the query goes out of the region |
| `{ "type": "objective", "id": objective }` | the objective is completed (or reopened) |
| `{ "type": "projectile", "id": launch id, "fate": "landed" | "downed" }` | a round fired by a `launch` action lands or is shot down |

The unit or building an event is about is **the event entity**: `{ "event": true }` in a query or an entity reference.

### Queries

A query picks living units and buildings; every field given must hold. `instance` (a placed or spawned instance id), `group`, `owner` (seat), `defId`, `prefix` (of the def id), `kind` ("unit" | "building"), `domain` ("ground", "air", "ship", "amphibious", "none" for buildings), `yard` (where it is made: "ground" | "air" | "ship"), `armed`, `hq`, `notMine` (not a mine), `complete` (not a building site), `outside` (not carried inside another), `region`, `variable` (the entity whose id the variable holds), `event: true` (the event entity), and `alive: false` to include the fallen.

### Actions

| action | fields | what it does |
| --- | --- | --- |
| `set` | `variable`, `value`: Expr | sets a variable |
| `add` | `variable`, `value`: Expr | adds to a number variable (a missing one counts as 0) |
| `if` | `condition`: Expr, `then`: Action[], `else`?: Action[] | runs one list or the other |
| `each` | `query`, `variable`, `actions`, `limit`? | runs `actions` once per match, with `variable` holding its entity id (read it with `{ "var": … }` or `{ "variable": … }` in a query or entity reference) |
| `call` | `id`: function, `args`?: { name: Expr } | runs a named action list from the scenario's `actions` table; `args` become variables inside it. A function may not call itself, directly or through another |
| `delay` | `seconds`: Expr, `actions` | runs `actions` that many seconds later (at least the next tick); saves keep it |
| `trigger` | `id`: trigger, `enabled`: bool | turns a trigger on or off |
| `spawn` | `instances`: Instance[], `group`? | places these exact units or buildings (Instance as in `instances`, world pixels); each instance id can then be queried with `{ "instance": id }` |
| `wave` | `owner`, `units`: [def id, count Expr][], `x`, `y`, `targetX`?, `targetY`?, `route`?, `attack`?, `group`?, `replaceGroup`? | a column of units appears around tile x,y and drives to the target tile (attack-moving unless `attack` is false) or along `route`; `group` collects them (added to the group unless `replaceGroup`) |
| `group` | `id`, `query`, `append`? | names the matching entities as a group for later queries (replaces it unless `append`) |
| `remove` | `query`, `reason`?: "removed" | "escaped" | takes the matches off the field without a kill |
| `damage` | `query`, `amount`: Expr | deals that much damage to every match |
| `order` | `query`, `order`: "move" | "attackMove" | "stop" | "hold", `x`?, `y`?, `route`? | orders the matches to a tile, or along a route |
| `entity` | `query`, `property`: "hp" | "locked" | "holdPos" | "holdFire" | "aggressive" | "xp" | "scriptedFire", `value` | sets a property on every match (`locked`: the player cannot command it; `scriptedFire`: its guns fire only by `launch`) |
| `metal` | `owner`, `amount`: Expr, `set`? | gives (or, with `set`, sets) a player's metal |
| `research` | `owner`, `ids`: string[] | grants research |
| `dress` | `owner`, `skins`?, `decals`? | changes a player's skins and decals |
| `objective` | `id`, `state`: "show" | "complete" | "update", `params`?: Expr[] | shows, completes or refreshes an objective (objectives are hidden until shown unless they start visible) |
| `announce` | `textKey`, `params`?: Expr[], `kind`?: "info" | "good" | "warn" | "bad", `owner`? | a line in the activity feed, for one seat or (without `owner`) everyone |
| `ping` | `x`, `y` (tiles), `color`?, `owner`? | a ping on the minimap |
| `sound` | `sound`, `x`?, `y`? | plays a game sound |
| `launch` | `id`, `from`: Ref, `target`: Ref, `weapon`: def id, `damage`: Expr, `speed`? | fires one round; its fate is read with `{ "projectile": id, … }` and the `projectile` event |
| `result` | `win`: bool, `winnerTeam`? | ends the match: `win` is the human players' result |

### Expressions

A value anywhere an Expr is asked for: a number, string or boolean, or one of

| expression | value |
| --- | --- |
| `{ "var": name }` | a variable (inside `each` and `call`, its local first) |
| `{ "time": true }` | seconds since the match opened |
| `{ "count": Query }` | how many match |
| `{ "random": Query }` | the id of one match, picked by the match's own dice (0 for none) |
| `{ "stat": { "owner", "field": "metal" \| "kills" \| "losses" \| "built" \| "trained" \| "pop" } }` | a seat's figure |
| `{ "entity": Ref, "property": "id" \| "hp" \| "x" \| "y" \| "alive" \| "owner" }` | one entity's figure (x and y in world pixels); Ref is an instance id, `{ "variable": name }` or `{ "event": true }` |
| `{ "objective": id }` | whether it is completed |
| `{ "canRebuild": seat }` | whether the seat still has a headquarters or a builder to raise one: `not` of it is "this player is beaten" |
| `{ "playerName": seat }` | the seat's player, named in a line's params |
| `{ "projectile": launch id, "fate": "flying" \| "landed" \| "downed" }` | whether a launched round is in that state |
| `{ "groupOutcome": group, "outcome": "destroyed" \| "escaped" \| "removed" }` | how many of the group ended that way |
| `{ "op": name, "args": Expr[] }` | `add` sum, `sub` first minus second, `mul` product, `div` first over second (0 when dividing by 0), `mod` remainder (0 by 0), `min` smallest, `max` largest, `floor` round down, `ceil` round up, `eq` equal, `ne` not equal, `lt` <, `lte` ≤, `gt` >, `gte` ≥, `all` every argument true, `any` some argument true, `not` not the first |

### Patterns

- **Watch a rule:** a `tick` event with `every: 1`, the rule as its `condition`, and `once: true` when it ends something.
- **Lose when beaten:** `{ "op": "not", "args": [{ "canRebuild": 0 }] }` → `result` with `win: false`.
- **Waves:** a `time` event with `at` and `every`, a `wave` variable `add`ed by 1, and unit counts as expressions of it; send them from a `scripted` seat.
- **Timers on the HUD:** a counter whose value is `max(0, ceil(duration − time))`.
- **A unit to protect:** place it as an instance, then a `destroyed` event with `{ "instance": id }`.
- **Scores:** variables, `add`ed in triggers and shown as counters.

## Campaigns

`{ "id", "name", "scenarios": game mode id[], "description"?, "carryVariables"?: name[], "branches"?: [{ "from", "to", "variable", "equals" }] }`: the modes are played in order; carried variables keep their values into the next; a branch picks the next mission by a variable's value at the end of `from`.

## Units and buildings

A `defId` is one of the game's own below, or a def of a unit pack in `mods`. A world names only what it carries, whatever mods a player has on.

| id | kind | name |
| --- | --- | --- |
| `aaturret` | building | AA Turret |
| `airbase` | building | Airbase |
| `airbase2` | building | Advanced Airbase |
| `airbase3` | building | Strategic Airbase |
| `arty` | unit | Thunder Howitzer |
| `bomber` | unit | Vulture Bomber |
| `btlship` | unit | Sovereign Battleship |
| `buggy` | unit | Recon Buggy |
| `bulwark` | unit | Bulwark Mobile Interrupter |
| `c47` | unit | Condor Cargo Plane |
| `cannonturret` | building | Cannon Turret |
| `cannonturret2` | building | Bastion Cannon |
| `carrier` | unit | Aircraft Carrier |
| `cormorant` | unit | Cormorant ASW Helicopter |
| `destroyer` | unit | Orca Destroyer |
| `drake` | unit | Drake Flame Tank |
| `drone` | unit | Scout Drone |
| `engboat` | unit | Engineer Boat |
| `engineer` | unit | Engineer |
| `extractor` | building | Metal Extractor |
| `extractor2` | building | Advanced Extractor |
| `extractor3` | building | Deep-Core Extractor |
| `factory` | building | War Factory |
| `factory2` | building | Advanced War Factory |
| `factory3` | building | Arsenal |
| `fighter` | unit | Falcon Fighter |
| `flak` | unit | Flak Track |
| `flameturret` | building | Flame Thrower |
| `frigate` | unit | Aegis Frigate |
| `gatling` | building | Gatling Turret |
| `gator` | unit | Gator Amphibious Combat Vehicle |
| `gunboat` | unit | Gunboat |
| `gunship` | unit | Spectre Gunship |
| `heli` | unit | Cobra Attack Helicopter |
| `hercules` | unit | Hercules Recovery Vehicle |
| `hornet` | unit | Hornet Suicide Drone |
| `hq` | building | Headquarters |
| `hq2` | building | Headquarters Lv.2 |
| `htank` | unit | Mammoth Heavy Tank |
| `interceptor` | building | Missile Interrupter |
| `interceptor2` | building | Strategic Interrupter |
| `jet` | unit | Thunderbolt Strike Jet |
| `kraken` | unit | Kraken Cruise-Missile Submarine |
| `landmine` | building | Land Mine |
| `ltank` | unit | Wolf Light Tank |
| `mboat` | unit | Missile Boat |
| `mbt` | unit | Bison Battle Tank |
| `mgturret` | building | MG Turret |
| `mjet` | unit | Albatross Naval Striker |
| `mlrs` | unit | Tempest MLRS |
| `moray` | unit | Moray Infiltration Submarine |
| `navyard` | building | Naval Yard |
| `navyard2` | building | Advanced Naval Yard |
| `navyard3` | building | Deepwater Yard |
| `nukesilo` | building | Nuclear Silo |
| `platform` | building | Offshore Rig |
| `platform2` | building | Advanced Offshore Rig |
| `platform3` | building | Deepwater Rig |
| `power` | building | Power Plant |
| `power2` | building | Advanced Power Plant |
| `power3` | building | Singularity Power Plant |
| `radar` | building | Radar Station |
| `radarcar` | unit | Owl Radar Car |
| `reactor` | building | Nuclear Reactor |
| `repairtower` | building | Repair Tower |
| `salamander` | unit | Salamander Thermobaric Mortar |
| `sam` | unit | Hawk SAM Launcher |
| `samsite` | building | SAM Site |
| `sandbag` | building | Sandbags |
| `scorpion` | unit | Scorpion Ballistic Missile Launcher |
| `seatrans` | unit | Landing Craft |
| `sub` | unit | Barracuda Submarine |
| `td` | unit | Viper Tank Destroyer |
| `theli` | unit | Pelican Transport Helicopter |
| `veil` | building | Veil EW Station |
| `warlab` | building | War Lab |
| `watermine` | building | Water Mine |
| `wraith` | unit | Wraith Stealth Bomber |

## Check

The game checks a world with its own validator, which ships as one file in the public mod registry. Run it after every change and fix every issue it lists (each has a path into the file):

```bash
curl -fsSLo steel-tide-world.mjs https://raw.githubusercontent.com/steel-tide/mods/main/tools/steel-tide-world.mjs
node --input-type=module -e "import { parseWorld } from './steel-tide-world.mjs'; import { readFileSync } from 'node:fs'; const r = parseWorld(readFileSync(process.argv[1], 'utf8')); console.log(r.ok ? 'ok' : r.errors.map(e => e.path + ': ' + e.message).join('\n')); process.exit(r.ok ? 0 : 1);" my-world.steel-tide-world
```

The validator checks the shape and every reference (maps, regions, routes, objectives, triggers, functions, def ids). It cannot tell whether the mode is fun or even winnable: read the program through once as a player would, asking what ends the match each way.

## Play it

In the game, open the world library (Editor → Worlds), Import the file, open the game mode and press **Test**. The World Editor shows the same file as modules — Layout, Triggers (each one as a sentence), Objectives, Briefing, Settings — and its problem list is the validator's. Exporting from the editor writes the file back.

## Example

A complete world: one map and one game mode, five waves down a road at a player who must hold. Every line is in all 3 languages.

```json
{
  "format": "steel-tide-world",
  "v": 1,
  "id": "crossroads-world",
  "name": "Crossroads",
  "version": "1.0.0",
  "author": "You",
  "license": "CC-BY-4.0",
  "description": "Hold the crossroads against five waves.",
  "maps": {
    "crossroads": {
      "format": "steel-tide-map",
      "v": 1,
      "name": "Crossroads",
      "w": 48,
      "h": 48,
      "terrain": "/wM1AwgEKAMIBCgDCAQLAwIGGwMIBAoDAwYbAwgECQMDBhwDCAQIAwMGLAMDBiwDAwYsAwMGLAMDBiwDAwYsAwMGLAMDBiwDAwYsAwMGLAMDBiwDAwYsAwMGLAMDBiwDAwYsAwMGLAMDBiwDAwYsAwMGLAMDBiwDAwYsAwMGLAMDBiwDAwYsAwMGLAMDBiwDAwYsAwMGLAMDBiwDAwYuAwEG/wNIAw==",
      "deposits": [
        {
          "x": 14,
          "y": 40
        },
        {
          "x": 8,
          "y": 34
        },
        {
          "x": 34,
          "y": 8
        },
        {
          "x": 40,
          "y": 14
        }
      ],
      "spawns": [
        {
          "x": 8,
          "y": 40
        },
        {
          "x": 40,
          "y": 8
        }
      ]
    }
  },
  "scenarios": [
    {
      "id": "hold",
      "name": "Hold the Crossroads",
      "map": "crossroads",
      "cfg": {
        "mode": "sandbox",
        "mapKey": "custom",
        "seed": 1,
        "fog": true,
        "popCap": 100,
        "startMetal": 1500,
        "players": [
          {
            "isHuman": true,
            "team": 0
          },
          {
            "isHuman": false,
            "team": 1,
            "scripted": true
          }
        ]
      },
      "resultPolicy": "scripted",
      "defaultBases": false,
      "mapUnits": false,
      "instances": [
        {
          "id": "headquarters",
          "defId": "hq",
          "owner": 0,
          "x": 256,
          "y": 1296
        },
        {
          "id": "engineer-1",
          "defId": "engineer",
          "owner": 0,
          "x": 320,
          "y": 1200
        },
        {
          "id": "engineer-2",
          "defId": "engineer",
          "owner": 0,
          "x": 384,
          "y": 1232
        }
      ],
      "regions": [
        {
          "id": "gate",
          "name": "The Gate",
          "x": 20,
          "y": 24,
          "w": 8,
          "h": 8,
          "marker": {
            "color": "#e8b861"
          }
        }
      ],
      "routes": [
        {
          "id": "road",
          "name": "The Road",
          "points": [
            {
              "x": 40,
              "y": 8
            },
            {
              "x": 24,
              "y": 24
            },
            {
              "x": 10,
              "y": 38
            }
          ],
          "attack": true
        }
      ],
      "variables": {
        "wave": 0,
        "waves": 5
      },
      "objectives": [
        {
          "id": "survive",
          "textKey": "hold.objective",
          "params": [
            {
              "var": "waves"
            }
          ]
        }
      ],
      "counters": [
        {
          "id": "wave",
          "textKey": "hold.counter",
          "value": {
            "var": "wave"
          }
        }
      ],
      "briefing": {
        "titleKey": "hold.title",
        "bodyKey": "hold.brief"
      },
      "triggers": [
        {
          "id": "opening",
          "name": "Opening",
          "event": {
            "type": "start"
          },
          "actions": [
            {
              "type": "objective",
              "id": "survive",
              "state": "show"
            },
            {
              "type": "ping",
              "x": 24,
              "y": 28,
              "color": "#e8b861",
              "owner": 0
            }
          ]
        },
        {
          "id": "waves",
          "name": "Send a wave",
          "event": {
            "type": "time",
            "at": 45,
            "every": 50
          },
          "condition": {
            "op": "lt",
            "args": [
              {
                "var": "wave"
              },
              {
                "var": "waves"
              }
            ]
          },
          "actions": [
            {
              "type": "add",
              "variable": "wave",
              "value": 1
            },
            {
              "type": "announce",
              "textKey": "hold.incoming",
              "params": [
                {
                  "var": "wave"
                }
              ],
              "kind": "warn",
              "owner": 0
            },
            {
              "type": "wave",
              "owner": 1,
              "units": [
                [
                  "buggy",
                  {
                    "op": "add",
                    "args": [
                      2,
                      {
                        "var": "wave"
                      }
                    ]
                  }
                ],
                [
                  "mbt",
                  {
                    "op": "floor",
                    "args": [
                      {
                        "op": "div",
                        "args": [
                          {
                            "var": "wave"
                          },
                          2
                        ]
                      }
                    ]
                  }
                ]
              ],
              "x": 40,
              "y": 8,
              "route": "road",
              "group": "raiders"
            }
          ]
        },
        {
          "id": "breach",
          "name": "Raiders at the gate",
          "event": {
            "type": "enter",
            "region": "gate",
            "query": {
              "owner": 1
            }
          },
          "once": true,
          "actions": [
            {
              "type": "announce",
              "textKey": "hold.breach",
              "kind": "bad",
              "owner": 0
            }
          ]
        },
        {
          "id": "held",
          "name": "Held",
          "event": {
            "type": "tick",
            "every": 1
          },
          "condition": {
            "op": "all",
            "args": [
              {
                "op": "gte",
                "args": [
                  {
                    "var": "wave"
                  },
                  {
                    "var": "waves"
                  }
                ]
              },
              {
                "op": "eq",
                "args": [
                  {
                    "count": {
                      "owner": 1,
                      "kind": "unit"
                    }
                  },
                  0
                ]
              }
            ]
          },
          "once": true,
          "actions": [
            {
              "type": "objective",
              "id": "survive",
              "state": "complete"
            },
            {
              "type": "result",
              "win": true
            }
          ]
        },
        {
          "id": "fallen",
          "name": "Base lost",
          "event": {
            "type": "tick",
            "every": 1
          },
          "condition": {
            "op": "not",
            "args": [
              {
                "canRebuild": 0
              }
            ]
          },
          "once": true,
          "actions": [
            {
              "type": "result",
              "win": false
            }
          ]
        }
      ]
    }
  ],
  "campaigns": [],
  "mods": [],
  "texts": {
    "hold.title": [
      "Hold the Crossroads",
      "守住十字路口",
      "교차로를 지켜라"
    ],
    "hold.brief": [
      "Raiders are coming down the road. Build up and hold until the last wave is gone.",
      "袭击者正沿着公路逼近。发展壮大，坚守到最后一波敌人覆灭。",
      "습격대가 도로를 따라 내려옵니다. 기지를 키우고 마지막 공격대가 사라질 때까지 버티십시오."
    ],
    "hold.objective": [
      "Survive {0} waves",
      "撑过 {0} 波进攻",
      "공격 {0}회 버티기"
    ],
    "hold.counter": [
      "Wave",
      "波次",
      "공격"
    ],
    "hold.incoming": [
      "Wave {0} is on the road",
      "第 {0} 波正沿公路而来",
      "공격 {0}이 도로로 다가옵니다"
    ],
    "hold.breach": [
      "Raiders are at the gate",
      "袭击者已到达关口",
      "습격대가 관문에 도착했습니다"
    ]
  }
}
```
