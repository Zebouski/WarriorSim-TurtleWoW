Setting up development environment
==================================
This project uses gulp 3.9.1 to build, and currently does not work with Node 12+.

On windows you should install [nvm-windows](https://github.com/coreybutler/nvm-windows) and tell it to use Node 11.15.0.

Install webapp (first time):
```bash
git clone https://github.com/Zebouski/WarriorSim-TurtleWoW.git
cd WarriorSim-TurtleWoW
nvm install 11.15.0
nvm use 11.15.0
npm install
```

Run webapp (every time):
```bash
nvm use 11.15.0
npm run dev
```

After making code changes and testing that they work, make sure to rebuild the dist/ files and commit them as well:
```bash
npm run dist
git add .
git commit
```

## Adding Items

gear/index.html has automated tools for wowhead.

### Manually

Open the `js/data/gear_turtle.js` file in your dev envorionment, or just the github web editor.

Example entry:
```
    {
      "name": "Sacrificial Gauntlets",
      "ac": 441,
      "crit": 1,
      "hit": 1,
      "str": 19,
      "source": "ZG",
      "phase": 4,
      "id": 22714,
      "i": 68,
      "q": 3,
    },
```
DPS Stats (`str`, `agi`, `crit`, `hit`, `ap`, `arp`, `haste`) are simply added as such.

Stamina as `sta` and Armor as `ac` placed at the top below name.

For source, check the Sim's UI for exact names used, including the raids like `BWL`, worldbosses by name `Kazzak`, and `Dungeon` `Crafting` `Quest` `PvP` or `Other`.

Phase relates to the 2019 classic phase the item came out in. We might deprecate this, but for now all new TWoW items should be marked phase 7.

`id`: The Item ID can be found via the Web DB, or ingame with this addon https://github.com/cyaohiri/ItemIDTooltip, or pfUI's Show Item ID option. All IDs must be unique per gear slot, or the Sim will bug out!

`i`: The Item Level can be found via the Web DB. For all items under 59, the iLvl will be the item's required level plus 5. Omit this entire line if unknown.

`q`: The quality of the item: 2 for green, 3 for blue, 4 for purple, 5 for legendary.

#### Sections

Please add new Turtle WoW items to the top of a section, underneath the opening `[` of the gear slot, and Blizzard items at the bottom, before the closing `]`.

Some sections are duplicated. Finger1 and Finger2, Trinket1 and Trinket2, these should always be exact duplicates! The users can figure out ingame which items are unique, the sim won't handhold someone selecting 2 same unique rings.

The Mainhand and Offhand section are mostly duplicates, but there are some Mainhand- and Offhand-only weapons, and only exist in these gear sections. These also need `"Mainhand": true,` and `"offhand": true,` respectively added to their data.

#### Weapons

These have a few more entires:
```
      "mindmg": 54,
      "maxdmg": 101,
      "speed": 1.5,
      "type": "Sword",
```
Types are `"Sword"` `"Axe"` `"Mace"` `"Dagger"` `"Polearm"` `"Fist"`

In case of those weapons that have "+ 16 - 30 Nature Damage" like Thunderfury, simply add this spell damage to the mindmg and maxdmg. We know this is inaccurate.

#### Weapon Skill

##### If there's only one type of skill on gear, add it like this: 

```
      "skill": 7,
      "type": "2H Axe",
```

(Do not specify 2H for Polearms and 1H for Daggers, its assumed)

##### If there is both 1H+2H skill of the same type on gear and nothing else, add it like this:
```
      "skill": 7,
      "type": "Axe",
```

##### If there are multiple types of various skills on gear or weapons, add it like this:
```
      "type": "1H Sword/Axe/Dagger",
      "skills": {
        "2": 7,
        "11": 7,
        "13": 7
      }
```
To find which of these skill IDs correspond to which type of weapon, go to the `js/classes/weapon.js` file and look at the first table of `WEAPONTYPE =`. Note that the Only-1H and Only-2H variants of sword axe and mace skill are marked as such. Fist Skill and Unarmed skill are the same in both the sim and in-game.

#### Resist Gear
One resist line:
```
      "resist": {
        "fire": 10,
      },
```
Multiple:
```
      "resist": {
        "fire": 10,
        "nature": 10,
        "arcane": 10,
        "frost": 15,
        "shadow": 5,
      },
```

If the item has pretty shitty DPS stats and would only ever be used as part of a resist set, add a `"subsource": "fire",` above the `"resist"` line. This item will not show up unless the user specifically enables the school of resist gear in the settings... so don't put this subsource line on otherwise good items.
