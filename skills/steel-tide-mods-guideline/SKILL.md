---
name: steel-tide-mods-guideline
description: Make, test and publish a mod for Steel Tide, the browser RTS — new units, buildings and upgrade levels in one mod.json. Use when asked to create or edit a Steel Tide mod, write its manifest or sprite sheets, validate it, or publish it to the steel-tide/mods registry.
license: MIT
---
# Making a Steel Tide mod: a brief for a coding agent

You are helping make a mod for Steel Tide (https://steelti.de), a browser real-time strategy game.
A mod adds units, buildings and upgrade levels. It cannot change the game's rules, its interface, or an existing unit or building: it only adds, and everything it adds is switched off with it.
Read this whole brief once, then work from the tables. When in doubt, prefer the smallest mod that plays.

## What a mod is

A folder:

```
my-mod/
  mod.json          the manifest: the mod's identity, its defs, and the sheets they draw with
  sprites/*.png     optional art (a def without any is drawn as a plain placeholder)
  screenshots/*.png the mod in play, 4:3 (the registry asks for at least one; see Publish)
  maps/*.steel-tide-map  optional maps from the Map Editor, offered in the setup screens while the mod is on (see Maps)
  README.md         optional
```

Published mods live in https://github.com/steel-tide/mods under `mods/<id>/`; the game lists that registry under Settings → Mods, and the website at https://steelti.de/mods.

## The manifest, minimal

One vanilla unit copied and re-tuned. `extends` inherits everything (art, weapons, where it is built) and every field you name overrides it:

```json
{
  "format": "steel-tide-mod",
  "v": 1,
  "id": "bison-ii",
  "name": [
    "Bison II",
    "野牛 II"
  ],
  "version": "1.0.0",
  "author": "you",
  "description": [
    "A heavier Bison for the late game.",
    "后期用的重型野牛。"
  ],
  "defs": [
    {
      "id": "bison2",
      "extends": "mbt",
      "name": [
        "Bison II",
        "野牛 II"
      ],
      "desc": [
        "Thicker plate, a bigger gun, and a bigger bill.",
        "更厚的装甲、更大的炮，也更贵。"
      ],
      "tier": 3,
      "cost": 520,
      "hp": 1100,
      "speed": 52,
      "requires": [
        "radar"
      ],
      "weapons": [
        {
          "id": "cannon",
          "cls": "cannon",
          "dmg": 90,
          "reload": 2,
          "range": 5,
          "splash": 12,
          "turret": true,
          "muzzleOffset": 23
        }
      ],
      "aiWeight": 2
    }
  ]
}
```

## The manifest, deeper

Own art, a defended building, and a fourth level for a vanilla building line:

```json
{
  "format": "steel-tide-mod",
  "v": 1,
  "id": "ironworks",
  "name": [
    "Ironworks",
    "铁工厂"
  ],
  "version": "1.0.0",
  "author": "you",
  "description": [
    "A hover tank, a bunker and a fusion plant.",
    "一辆悬浮坦克、一座碉堡和一座聚变电站。"
  ],
  "license": "CC-BY-4.0",
  "defs": [
    {
      "id": "ironworks-hover",
      "name": [
        "Skimmer Hover Tank",
        "掠行悬浮坦克"
      ],
      "desc": [
        "Fast, thin-skinned, rockets.",
        "快、皮薄、打火箭。"
      ],
      "kind": "unit",
      "domain": "ground",
      "tier": 2,
      "cost": 260,
      "hp": 320,
      "armor": "light",
      "speed": 110,
      "turnRate": 5,
      "radius": 9,
      "body": {
        "r": 10,
        "len": 10
      },
      "trail": "tire",
      "weapons": [
        {
          "id": "pods",
          "cls": "rocket",
          "dmg": 16,
          "reload": 2.2,
          "range": 4.5,
          "burst": 4,
          "burstDelay": 0.1,
          "splash": 10,
          "spread": 14
        }
      ],
      "producedBy": [
        "factory2",
        "factory3"
      ],
      "aiWeight": 1
    },
    {
      "id": "ironworks-bunker",
      "name": [
        "Bunker",
        "碉堡"
      ],
      "desc": [
        "A gun pit that takes a beating.",
        "扛打的火力点。"
      ],
      "kind": "building",
      "cost": 380,
      "hp": 1600,
      "fw": 2,
      "fh": 2,
      "power": -3,
      "vision": 8,
      "weapons": [
        {
          "id": "gun",
          "cls": "autocannon",
          "dmg": 22,
          "reload": 0.5,
          "range": 5,
          "turret": false,
          "targets": [
            "ground",
            "ship"
          ]
        }
      ]
    },
    {
      "id": "ironworks-fusion",
      "name": [
        "Fusion Plant",
        "聚变电站"
      ],
      "desc": [
        "The plant line's fourth level.",
        "发电厂线的第四级。"
      ],
      "extends": "power3",
      "upgradeOf": "power3",
      "cost": 2200,
      "hp": 2600,
      "power": 500,
      "upgradeCost": 1100,
      "upgradeTime": 60,
      "requires": [
        "radar",
        "reactor"
      ]
    }
  ],
  "sprites": [
    {
      "key": "u.ironworks-hover",
      "file": "sprites/u.ironworks-hover.png",
      "frames": 1,
      "rotated": true,
      "fw": 24,
      "fh": 26
    },
    {
      "key": "u.ironworks-bunker",
      "file": "sprites/u.ironworks-bunker.png",
      "frames": 1
    }
  ],
  "screenshots": [
    "screenshots/skimmers.png"
  ]
}
```

## Fields

Anything not in these tables is ignored with a warning. Numbers outside the stated range are errors.

### mod.json (top level)

| field | type | required | default | meaning |
| --- | --- | --- | --- | --- |
| `format` | string | yes |  | always "steel-tide-mod" |
| `v` | integer 1–1 | yes |  | format version, 1 |
| `id` | id | yes |  | the mod's id: lower case, letters, digits, `-`; also its folder in the registry |
| `name` | text | yes |  | the name shown in the mod list |
| `version` | string | yes |  | e.g. `1.0.0`; the game offers an update when the registry's is newer |
| `author` | string |  |  | who made it |
| `description` | text |  |  | one or two sentences for the list |
| `homepage` | string |  |  | a link: a repository, a thread |
| `license` | string |  | CC-BY-4.0 | the mod's licence (SPDX id) |
| `minGame` | string |  |  | the oldest game version it is written for |
| `defs` | def[] | yes |  | the units, buildings and upgrade levels |
| `sprites` | sheet[] |  |  | the sheets the defs draw with (see below) |
| `sounds` | string |  |  | the recordings the weapons fire with (see below) |
| `screenshots` | path[] |  |  | pictures of the mod in play, relative to mod.json (`screenshots/1.png`): 4:3, at least 640 px wide, under 2 MB each, up to 8. The registry asks for at least one and shows them on the mod's page |
| `maps` | string |  |  | maps the mod carries, relative to mod.json (`maps/city.steel-tide-map`): files exported from the Map Editor, up to 8, each under 512 KB. A map may stand the mod's own units; the setup screens offer it beside the player's own map while the mod is on |
| `files` | { path: dataURL } |  |  | single-file form only: the sheets, sounds and maps, embedded as data URLs by path |

`name`, `description` and every `desc`/`name` on a def are *text*: a string (used for every language), `["English", "中文", "한국어"]` in that order, or `{ "en": "…", "zh": "…", "ko": "…" }`. Only English is required; a language left out reads as English.

### A def (`defs[]`)

| field | type | required | default | meaning |
| --- | --- | --- | --- | --- |
| `id` | id | yes |  | unique across every mod and the vanilla roster; prefix a generic word with your mod's id |
| `name` | text | yes |  | as the HUD shows it |
| `desc` | text |  | "" | the tooltip line |
| `extends` | id |  |  | a vanilla def id (or an earlier def of this mod) to copy, then override field by field; inherits its art and where it is built |
| `kind` | unit \| building | yes |  | a unit or a building (required unless `extends` says) |
| `domain` | ground \| ship \| air \| amphibious | units only | ground | where it moves; a submarine is a `ship` with `underwater`, and an `amphibious` hull drives and swims (a ship target afloat, a ground one ashore) |
| `tier` | integer 1–3 |  | 1 | the factory level it appears at, and the badge |
| `cost` | number 0–99999 | yes |  | metal |
| `buildTime` | number 0–3600 |  | cost ÷ 14 | seconds at full power |
| `pop` | integer 0–50 |  | 1 for a unit, 0 for a building | population it counts for |
| `hp` | number 1–1000000 | yes |  | hit points |
| `armor` | light \| medium \| heavy \| ship \| sub \| air \| structure |  | by domain | the armour class weapons are multiplied against |
| `speed` | number 0–1000 | units only | 60 | world px/s (a tile is 32) |
| `turnRate` | number 0–50 | units only | 3.5 | rad/s |
| `vision` | number 0–64 |  | 8 | sight, in tiles |
| `radius` | number 1–200 |  | 9, or the footprint | collision radius, world px, and the *combat* yardstick: range, splash and hits are measured to it |
| `body` | { r, len } | units only | the circle of `radius` | the room the hull takes up when units push each other apart, world px: `r` its half-width, `len` its length less the two round ends. A long hull is what a circle cannot say. A ship whose beam (`r` twice) is over a tile (32) needs that many tiles of water abeam, rounded up: it never enters a channel narrower than itself |
| `weapons` | weapon[] |  | [] | the weapons (see below); an empty list is unarmed |
| `fw` | integer 1–8 | buildings only | 2 | footprint width, tiles |
| `fh` | integer 1–8 | buildings only | 2 | footprint height, tiles |
| `producedBy` | id[] | units only | the line for its domain, from its tier up | the buildings whose production list it joins (vanilla or this mod's) |
| `produces` | id[] | buildings only |  | a factory: the units it builds |
| `builtBy` | id[] | buildings only | ["engineer"] | the builder units that may place it |
| `builds` | id[] | units only |  | a builder unit: the buildings it can construct |
| `buildRate` | number 0–10000 | units only |  | a builder unit: hp of work per second |
| `reach` | number 0–256 | units only |  | a builder unit: how far past a target's radius it works from, world px (the engineer's 46 when unset; the engineer boat's 64) |
| `power` | number -10000–10000 |  | −pop for a unit, 0 for a building | positive produces, negative draws; every unit draws its population |
| `metalRate` | number 0–1000 | buildings only |  | metal per second (an extractor) |
| `needsDeposit` | boolean | buildings only |  | must stand on a deposit |
| `wall` | boolean | buildings only |  | a wall: one tile that joins its neighbours of the same def (its sheet is sixteen frames, one per set of neighbours: north 1, east 2, south 4, west 8), the last thing a gun shoots at, untouched by a pulse, and it falls without a charge |
| `repairRange` | number 0–64 | buildings only |  | a repair aura, tiles |
| `repairRate` | number 0–10000 | buildings only |  | hp per second per target |
| `repairTargets` | integer 1–50 | buildings only |  | targets served at once |
| `upgradeOf` | id | buildings only |  | the building this is the next level of; that one gains the upgrade button |
| `upgradeCost` | number 0–99999 | buildings only | cost − the source's cost | with `upgradeOf`: the upgrade's price |
| `upgradeTime` | number 0–3600 | buildings only | buildTime | with `upgradeOf`: seconds |
| `requires` | id[] |  |  | building ids that must stand before it can be built |
| `nukeCapacity` | integer 0–10 |  |  | a launcher: warheads it holds |
| `nukeCost` | number 0–99999 |  |  | a launcher: metal per warhead |
| `nukeTime` | number 0–3600 |  |  | a launcher: seconds per warhead |
| `hatch` | number 0–30 |  |  | a launcher: seconds its doors take to open before the warhead leaves, and to close after; none fires on the spot |
| `antiWarhead` | boolean |  |  | its anti-air may fire on a nuclear warhead without a veteran's rank |
| `interceptRange` | number 0–64 |  |  | point defence: reach in tiles |
| `interceptMag` | integer 1–200 |  |  | point defence: rounds ready |
| `interceptReload` | number 0.05–600 |  |  | point defence: seconds per round replaced |
| `interceptMuzzleOffset` | number 0–200 |  |  | point defence: launcher length, world px |
| `transportCap` | integer 1–50 | units only |  | a transport: hold, in cargo weight |
| `cargoReach` | number 0.5–4 | units only | 1 | a transport: how far its load and unload distances stretch, as a multiple of the default (the landing craft's and the Moray's 1.1) |
| `landsForCargo` | boolean | units only |  | a cargo plane that touches down to load |
| `cargoWeight` | number 0–50 | units only | pop | how much of a hold it takes |
| `underwater` | boolean | units only |  | a submarine: seen only by sonar |
| `sonar` | number 0–64 | units only |  | sonar range, tiles |
| `stealth` | number 0–64 | units only |  | seen only within this many tiles of an enemy |
| `detect` | number 0–64 |  |  | reveals stealth within this many tiles |
| `radar` | boolean |  |  | sees by radio: the sight circle ignores weather and the hour, and drops to 7 tiles when short of power |
| `hovers` | boolean | units only |  | an aircraft that hovers instead of orbiting |
| `altitude` | number 0–64 | units only | 12 | an aircraft's drawn height, px |
| `fireOnMove` | boolean | units only |  | keeps shooting on a plain move |
| `burnMult` | number 0–4 | units only | 1 | what fire on the ground does to it, as a multiplier (the Drake's 0.5) |
| `trail` | tread \| tire \| wake \| none | units only | by domain | the mark it leaves, and the sound of it: treads and tyres are heard rolling; `none` for a unit on foot or a hovercraft, which neither cuts a rut nor is heard as an engine |
| `shadow` | boolean |  | true | the shadow the game casts for it: a unit's hull and turret silhouettes, a step to the south-east on the ground, further off in the air; a building's gun's, on its roof; `false` when the art brings its own, or none is wanted (a submarine casts none) |
| `sprite` | string |  | this mod's u.<id> sheet, else the base's art | the body's atlas key: one of this mod's sheets, or a vanilla key to borrow its art |
| `turretSprite` | string |  | this mod's tur.<id> sheet, else the base's (when its art is kept) | the rotating part's key, if any |
| `decals` | decal[] |  | the base's, when its art is kept | pictures laid on the hull and the turret besides their own sheets (see below), up to 12 |
| `aliases` | string[] |  |  | other names the console's `give` accepts |
| `aiWeight` | number 0–10 | units only | 0 | how readily the AI builds it: a Bison is 3, a scout car 1; 0 never |

Defaults when `extends` is absent: a unit is `domain: "ground"`, `tier: 1`, `pop: 1`, `speed: 60`, `turnRate: 3.5`, `vision: 8`, `radius: 9`, no `body` (units part on the circle of `radius`), armour by domain (ground and amphibious `medium`, ship `ship`, air `air`), a tread trail on land (`trail: "none"` for a unit on foot, which is then not heard rolling either) and a wake at sea; a building is `fw: 2, fh: 2`, `armor: "structure"`, `power: 0`, `pop: 0`. `buildTime` defaults to cost ÷ 14 seconds.
Where a unit is built when `producedBy` is absent: its domain's line from its tier up (ground: T1 → factory+factory2+factory3, T2 → factory2+factory3, T3 → factory3; ship: T1 → navyard+navyard2+navyard3, T2 → navyard2+navyard3, T3 → navyard3; air: T1 → airbase+airbase2+airbase3, T2 → airbase2+airbase3, T3 → airbase3; amphibious: T1 → factory+navyard+factory2+navyard2+factory3+navyard3, T2 → factory2+navyard2+factory3+navyard3, T3 → factory3+navyard3).
An `upgradeOf` def becomes upgrade-only (never placed directly): the named building gains an Upgrade button that turns it into this def, at `upgradeCost` over `upgradeTime`. A building may have only one next level, so `upgradeOf` can name a vanilla building at the end of its line (`power3`, `factory3`, `extractor3`, `gatling`, `cannonturret2`, `samsite`, `interceptor2`, `radar`, `repairtower`, `reactor`, `nukesilo`) or one of this mod's.

### A weapon (`defs[].weapons[]`)

| field | type | required | default | meaning |
| --- | --- | --- | --- | --- |
| `id` | string |  | w1, w2… | a name for the weapon |
| `cls` | mg \| autocannon \| cannon \| at \| he \| rocket \| navgun \| ashm \| torpedo \| aa | yes |  | what it was built to kill; picks its row of the armour matrix |
| `dmg` | number 0–100000 | yes |  | damage per hit |
| `reload` | number 0.05–600 | yes |  | seconds between shots or bursts |
| `range` | number 0.5–64 | yes |  | tiles |
| `minRange` | number 0–64 |  |  | tiles it cannot fire inside |
| `projectile` | bullet \| shell \| missile \| rocket \| bomb \| torpedo \| flak \| flame |  | by class | the round drawn |
| `speed` | number 1–5000 |  | by projectile | round speed, world px/s |
| `targets` | (ground \| ship \| sub \| air)[] |  | by class | what it may fire at: ground, ship, sub, air |
| `mult` | { armour: number } |  |  | overrides of the class row, by armour class |
| `splash` | number 0–500 |  |  | blast radius, world px |
| `burst` | integer 1–32 |  |  | shots per burst |
| `burstDelay` | number 0–5 |  |  | seconds between the shots of a burst |
| `homing` | boolean |  |  | the round tracks its target |
| `interceptable` | boolean |  |  | point defence may shoot it down |
| `arc` | boolean |  |  | a ballistic arc (artillery) |
| `turret` | boolean |  | true when the def has a turretSprite | fired from the rotating part |
| `muzzleOffset` | number 0–200 |  |  | pivot to muzzle, world px; a unit's art is drawn 1.5625×, so measure on the sheet and multiply |
| `bores` | integer 1–8 |  | 1 | barrels, fired one after another |
| `boreSpacing` | number 0–60 |  |  | gap between adjacent barrels, world px |
| `spread` | number 0–200 |  |  | inaccuracy at full range, world px |
| `friendlyFire` | boolean |  |  | the blast hurts your own side too |
| `fan` | number 0–1.5 |  |  | a `flame` jet: the half-angle it is sprayed across, radians |
| `burn` | number 0–1000 |  |  | a `flame` jet: damage a second the fire it leaves does, to both sides |
| `burnLife` | number 0–120 |  |  | a `flame` jet: seconds that fire keeps burning |
| `sound` | string |  | by class | the firing sound: mg, autocannon, cannon, missile, flak, arty, rocket, torpedo, bomb, flame, or the key of one of this mod's `sounds` |

The damage a weapon does is `dmg × the armour matrix cell for (cls, target armour)`, with `mult` overriding single cells. The matrix:

```
mg          light ×1.5  medium ×0.8  heavy ×0.4  structure ×0.35  ship ×0.5  air ×0.7
autocannon  light ×1.5  medium ×1  heavy ×0.5  structure ×0.5  ship ×0.7
cannon      light ×0.6  medium ×1.3  heavy ×1  structure ×0.8  ship ×0.9
at          light ×0.4  medium ×1.1  heavy ×1.8  structure ×0.7  ship ×1
he          light ×1.2  medium ×1  heavy ×0.75  structure ×1.6  ship ×1
rocket      light ×1.4  medium ×1.4  heavy ×0.9  structure ×0.7  ship ×0.9
navgun      light ×1  medium ×1  heavy ×0.75  structure ×0.75  ship ×1
ashm        ship ×1.6
torpedo     ship ×1.3  sub ×1.2
aa          air ×1
```

### A decal (`defs[].decals[]`)

| field | type | required | default | meaning |
| --- | --- | --- | --- | --- |
| `sprite` | string | yes |  | the sheet drawn: one of this mod's `dec.<name>` sheets, or any body or turret key |
| `on` | hull \| turret |  | hull | what it is fixed to and turns with; `turret` needs a `turretSprite` |
| `layer` | under \| hull \| over |  | hull, or over when on a turret | where it goes in the stack: under the body, on the hull under the turret, or over everything |
| `x` | number -256–256 |  | 0 | where it sits on the part's up-facing art: px right of the pivot, in the sheet's own px at its `fw`×`fh` size (the game scales it with the unit) |
| `y` | number -256–256 |  | 0 | px down from the pivot, toward the tail |
| `angle` | number -360–360 |  | 0 | turned this much further than what it is fixed to, degrees clockwise |
| `upright` | boolean |  |  | never turned: laid screen-up wherever its anchor is |
| `scale` | number 0.05–8 |  | 1 | drawn at this multiple of its sheet's size |
| `alpha` | number 0–1 |  | 1 | opacity |
| `when` | always \| moving \| still \| firing \| damaged \| night |  | always | shown only while the unit moves or stands (`moving`, `still`), within a reload of its last shot (`firing`), under half health (`damaged`), or after dusk (`night`: laid over the dark like a lamp, and the game's own headlights stay off a unit that has one) |

A decal is a picture laid on a def besides its body and its turret — a hatch, a crew figure, an outline, a lamp — fixed to the hull or the turret (`on`) and turned with it. Up to 12 on a def, drawn in the order listed within a layer. `x`/`y` are a point on that part's up-facing art: right and down (toward the tail) of its pivot, in the px of the sheet at its `fw`×`fh` size; the game scales them with the unit. The sheet is one of the mod's own under a `dec.<name>` key (see Art), or a body or turret key it borrows. A `night` decal is a lamp: laid over the dark rather than dimmed by it, so a headlight or a lit window reads as light, and a unit that carries one gets none of the game's own headlights. Paint a beam soft — a wide, faint falloff, not a hard-edged triangle — or it reads as glass.

### A sheet (`sprites[]`)

| field | type | required | default | meaning |
| --- | --- | --- | --- | --- |
| `key` | string | yes |  | `u.<id>` for a body, `tur.<id>` for a rotating part, `dec.<name>` for a decal; never a vanilla key |
| `file` | string | yes |  | the image, relative to mod.json (PNG, WebP or JPEG) |
| `frames` | integer 1–64 |  | 1 | animation frames, left to right in one strip |
| `fw` | number 4–512 |  | the footprint (a building) or the image | in-game frame width, whole world px |
| `fh` | number 4–512 |  |  | in-game frame height, whole world px |
| `rotated` | boolean |  |  | one up-facing image; the game bakes the 24 headings (hulls, turrets) |
| `pivotX` | number 0–1 |  | 0.5 | rotation pivot, as a fraction of the frame |
| `pivotY` | number 0–1 |  | 0.5 | rotation pivot, as a fraction of the frame |
| `anchorY` | number 0–512 |  |  | px from the top to the footprint centre (tall buildings) |
| `mount` | [x, y] |  |  | a body: where its turret sits (or, with a repair aura, where the beam leaves), as `[fx, fy]` |
| `fps` | number 0–60 |  |  | animation speed |
| `teams` | boolean |  | true | recolour magenta per faction; `false` draws the sheet as painted for every faction, and for the wreck |
| `ss` | integer 1–4 |  |  | supersample factor; omit to let the game choose |
| `fitFootprint` | boolean |  |  | scale the drawn content to fill the frame |
| `animRegion` | [x0, y0, x1, y1] |  |  | where the animation lives, `[x0, y0, x1, y1]` fractions; the rest is frozen |
| `stabilize` | boolean |  | true | re-align drifting frames |
| `freezeStatic` | boolean |  |  | median-freeze pixels that barely change |
| `stripBg` | boolean |  |  | force background removal on or off |
| `bgMinLuma` | number 0–255 |  |  | lightest colour still taken as background |
| `artifactCleanup` | boolean |  |  | sweep specks left by background removal |

### A sound (`sounds[]`)

| field | type | required | default | meaning |
| --- | --- | --- | --- | --- |
| `key` | string | yes |  | `<mod id>-<name>`, lower case; what a weapon's `sound` names |
| `file` | string | yes |  | the recording, relative to mod.json. An MP3 plays everywhere; WAV works, OGG not on Safari. Dry, close, under a second |

A weapon fires with the game's sound for its class unless its `sound` names one of these keys. A recording is a dry, close-miked one-shot under a second with no reverb tail; the engine attenuates and pans it by distance, and forty overlapping echoes turn to mud. Mono MP3 is the safe format.

## Maps

A mod may carry maps: files the game's Map Editor exports (`.steel-tide-map`), named under `maps` in mod.json, up to 8 of them and each under 512 KB. While the mod is on, the Conquest setup and the lobby offer them as cards beside the player's own map; choosing one plays it exactly as painted. A map may stand the mod's own units and buildings before the whistle: with the mod on, they are in the editor's forces palette, and the map reads on any game that has the mod (the registry checks it against the mod's own defs, not the vanilla roster). A scene laid this way — a base built up, a horde at the gate — is how a mod shows what it is without a word.

## Units of measure

- A tile is 32 world pixels. `speed` and projectile `speed` are world px/s; `range`, `minRange`, `vision`, `sonar`, `stealth`, `detect`, `repairRange` and `interceptRange` are tiles; `radius`, `body.r`, `body.len`, `splash`, `spread`, `muzzleOffset` are world px.
- Times are seconds: `buildTime`, `reload`, `burstDelay`, `upgradeTime`, `interceptReload`, `nukeTime`.
- For scale: the Bison main battle tank is cost 280, hp 620, speed 60, radius 10, body `{ r: 13, len: 12 }`, a cannon of dmg 60 every 1.8 s at range 4.6; a scout car is cost 60, hp 150, speed 120; a war factory is 3×3 tiles, hp 1500, power −8.

## Art

- Keys: a def's body is `u.<id>`, a rotating turret `tur.<id>`. A def with no sheet of its own may borrow vanilla art by naming a vanilla key in `sprite` (e.g. `"sprite": "u.mbt", "turretSprite": "tur.mbt"`); with `extends` it inherits the base's art. A mod may not replace vanilla art.
- A sheet is one PNG (WebP and JPEG are accepted): `frames` animation frames left to right in one horizontal strip, evenly spaced, no gaps, no borders.
- Hulls, turrets and everything that turns: draw ONE image facing UP and set `"rotated": true`; the game bakes the 24 headings. `fw`/`fh` are the in-game size of that up-facing image in world px (a tank hull is about 24×24; the image itself may be any resolution, 2–4× is best). `pivotX`/`pivotY` put the pivot on the turret ring (default centre).
- Buildings: one strip of frames, not rotated, drawn with a slight top-down southern tilt. The footprint is the bottom `fw×32` by `fh×32` px of the frame; anything above overhangs the terrain behind (towers, masts). Width, height and anchor are sized from the def's footprint automatically.
- A building with a gun that turns is two sheets like a hull and turret: the body with an empty ring and `"mount": [fx, fy]` saying where the ring sits as fractions of the frame, and a `tur.<id>` sheet for the gun; its weapons carry `"turret": true`.
- A decal's sheet (`dec.<name>`) is drawn like a turret's: one up-facing image, or a strip of `frames` at `fps` for something that moves (a spinning barrel, a blinking light), sized by `fw`/`fh` and drawn about its centre. Static detail belongs in the body's own sheet; a decal is for what the body cannot hold — a part that turns with the turret, a hatch the barrel sweeps over, a light that comes on at night, a barrel that spins while it fires.
- Faction colour: paint team-coloured parts in pure magenta (highlight #FF66FF, base #FF00FF, shadow #990099) and use magenta nowhere else; the game recolours it per player.
- Style: crisp pixel art, hard edges, no anti-aliasing, a muted military palette (DawnBringer-32), dark #222034 outlines. Generated sheets are cleaned automatically (background removal, frame registration), but a transparent background is best.

## The vanilla roster (ids you may `extends`, name in `producedBy`/`builtBy`/`requires`/`upgradeOf`, or borrow art from)

- ground units: engineer (T1, 200), buggy (T1, 60), ltank (T1, 120), mbt (T2, 280), htank (T3, 900), td (T2, 320), flak (T1, 170), sam (T2, 400), arty (T2, 420), mlrs (T3, 760), radarcar (T3, 480), salamander (T3, 800), bulwark (T3, 650), drake (T2, 300)
- ships: gunboat (T1, 130), mboat (T1, 280), frigate (T2, 600), destroyer (T2, 700), sub (T2, 480), btlship (T3, 1700), seatrans (T1, 220), engboat (T1, 200), kraken (T3, 1000), moray (T3, 700)
- amphibious units: gator (T2, 240)
- aircraft: drone (T1, 40), fighter (T2, 380), heli (T1, 340), jet (T2, 400), mjet (T2, 520), bomber (T2, 1000), theli (T1, 280), c47 (T2, 650), gunship (T3, 1600), wraith (T3, 1400), cormorant (T3, 700)
- buildings: hq (T1, 2500), extractor (T1, 120), extractor2 (T2, 310, upgrade level), extractor3 (T3, 810, upgrade level), power (T1, 140), power2 (T2, 400, upgrade level), power3 (T3, 1100, upgrade level), factory (T1, 320), factory2 (T2, 740, upgrade level), factory3 (T3, 1640, upgrade level), airbase (T1, 350), airbase2 (T2, 830, upgrade level), airbase3 (T3, 1780, upgrade level), navyard (T1, 380), navyard2 (T2, 800, upgrade level), navyard3 (T3, 1750, upgrade level), mgturret (T1, 130), gatling (T2, 390, upgrade level), cannonturret (T1, 320), cannonturret2 (T2, 740, upgrade level), aaturret (T1, 240), samsite (T2, 560, upgrade level), interceptor (T2, 450), interceptor2 (T3, 1100, upgrade level), sandbag (T1, 40), repairtower (T1, 360), radar (T2, 400), reactor (T3, 1400), nukesilo (T3, 1800)

## Test

1. Validate: in a clone of the registry, `node tools/check.mjs path/to/my-mod` prints every error with its path into mod.json. (No clone? The game says the same things when the mod is loaded.)
2. Load it in the game: Settings → Mods → *Open folder…* (Chrome keeps the folder open, so *Reload* re-reads your edits) or *Upload file…* (a zip of the folder, or a `.steel-tide-mod` file). Errors are listed on the spot; a loaded mod says how many units and buildings it added.
3. Play: start a Conquest match. Your units are in the factory's production list from the level you gave them; buildings are in the engineer's build menu; an upgrade level is on its building's Upgrade button. The console (backquote) has `give <id>` for anything. Mods apply to Conquest and the campaign; a networked match is always unmodded.
4. From a static server: `npx serve --cors path/to/my-mod` then open `https://play.steelti.de/?mod=http://localhost:3000/` — the mod is fetched at every boot, so a page reload picks up an edit.

## Publish

1. Fork https://github.com/steel-tide/mods, add your folder as `mods/<id>/` (the folder name is the mod's `id`), run `node tools/check.mjs mods/<id>`, open a pull request.
2. A published mod carries at least one screenshot of it in play, named under `screenshots` in mod.json: 4:3 (1600×1200 is a good size), at least 640 px wide, PNG, JPEG or WebP under 2 MB, up to 8 of them. The game's Settings → Mods page and https://steelti.de/mods show them; the first is the mod's card. Take them in a match with the mod's units on screen (the console's `give` puts them there) and crop to 4:3.
3. CI runs the same check on the whole registry: ids must be unique across every published mod, so prefix a generic word with your mod's id (`ironworks-bunker`, not `bunker`).
4. Once merged, the index is rebuilt and the mod appears in the game's registry list and at https://steelti.de/mods, where its installs are counted. Bump `version` for every change; the game offers the update.

## Rules

- A mod adds; it does not change vanilla defs, art, rules or interface. Do not try to override a vanilla id or key; the validator refuses it.
- Keep it honest: a unit needs a counter. The armour matrix is how the game makes one; give a new weapon a class it belongs to rather than an override on every cell.
- A mod is published under the licence its manifest names (default CC-BY-4.0). Only ship art you have the right to.
