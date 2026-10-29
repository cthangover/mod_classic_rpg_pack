# Classic RPG Rules — a ready set of fantasy rules

Classic RPG Rules is a rules pack: it contains no code, only data. It gives a game the familiar fantasy set — six attributes, health and mana, three classes, three equipment slots, eight pieces of gear, a strength tonic and four battle skills. RPG Plus is built on it; a visual novel could take it only for charisma checks.

The rules work through other mods. Stats Engine computes the numbers and shows the sheet, Equipment makes the gear wearable, Items Base keeps it in the bag, Characters Engine holds the skill cards, and Battle Formulas makes the skills' formulas work in battle. This pack only says *what* there is.

## In the game

A rogue of level 2 in RPG Plus with the iron sword:

![The character sheet of a level 2 Rogue in RPG Plus: Strength 8, Agility 12, Intelligence 5, Vitality 6, Charisma 9, Luck 9; Health 34/34, Mana 14/14, Attack 18 (+8), Armour 3, Magic 7; the Iron sword (ATK +5) in the Weapon slot.](assets/rogue-sheet.jpg)

Every number comes from this pack. Agility is the rogue's `8 + level * 2` = 12. Attack is the pack's `str + level` = 8 + 2 = 10, plus the rogue's trait `floor(agi / 4)` = 3, plus the iron sword's 5 — **18**, of which **+8** are bonuses. Strength 8 is the rogue's `5 + level` = 7 and one strength tonic.

## What is in the pack

**Attributes** (group *Attributes*, default 5): Strength `str`, Agility `agi`, Intelligence `int`, Vitality `vit`, Charisma `cha`, Luck `luk`.

**Combat stats** (group *Combat*), computed from the attributes:

```json
{ "Id": "hp",  "Formula": "12 + vit * 3 + level * 2", "Binds": "health" },
{ "Id": "mp",  "Formula": "int * 2 + level * 2", "Pool": true },
{ "Id": "atk", "Formula": "str + level", "Binds": "attack" },
{ "Id": "def", "Formula": "floor(vit / 2)", "Binds": "defence" },
{ "Id": "mag", "Formula": "int + level" }
```

Health, attack and defence become the battle numbers; mana is spent by skills and refilled.

**Classes** — each grows its attributes with the level, has a trait and learns skills:

- **Warrior** — strong and tough (`str 7 + level * 2`, `vit 6 + level * 2`), +10% health; Power strike at level 1, Guard break at level 3.
- **Mage** — `int 8 + level * 2`, +25% mana; Fireball at level 1, Heal at level 2.
- **Rogue** — `agi 8 + level * 2`, charisma and luck `7 + level`, attack + a quarter of agility; Power strike at level 1, Guard break at level 2.

**Slots**: Weapon (sword, staff, dagger), Body (armor, robe), Accessory (charm, ring).

**Gear and a tonic**:

![The pack's items: wooden sword, iron sword, oak staff, dagger, leather armour, robe, clover charm, silver ring, strength tonic.](assets/gear.jpg)

- Wooden sword: attack +2. Iron sword (warrior, rogue): attack +5. Oak staff (mage): magic +4, mana +5. Dagger: attack +3, agility +2.
- Leather armour: defence +2, health +6. Robe: mana +6, intelligence +1.
- Clover charm: luck +3. Silver ring: charisma +2.
- Strength tonic: strength +1 for good.

**Skills** — battle actions whose numbers are formulas (`a` is the one acting, `b` the target):

![The four skill cards: Power strike, Fireball, Heal, Guard break.](assets/skills.jpg)

```json
{ "Id": "classic/fireball", "Type": "ToEnemy",
  "Values": [ "RequiredPoint=1", "Cost=mp:4", "Pierce=1", "Formula=a.mag * 2 + rand(1, 6)" ] }
```

- Power strike: `a.atk * 1.5 + rand(0, a.str / 2)`.
- Fireball: costs 4 mana, ignores armour, `a.mag * 2 + rand(1, 6)`.
- Heal: costs 3 mana, heals self by `a.mag * 2 + 4`.
- Guard break: ignores armour, `a.atk + b.defence * 2`, 35% chance to stun for a turn.

## How RPG Plus uses it

- The prologue's first answer gives the hero one of the three classes and its starting weapon.
- The village merchant sells the swords, dagger, staff and armour (RPG Plus adds them to the shop with a patch).
- The training yard gives a strength tonic; the seer gives the clover charm.
- Battles use the skills through Battle Formulas, in the default battle system and in the card one.

## Using it in your game

Add `classic_rpg_pack` to the game's collection; the mods it depends on are loaded with it. Then give a character a class with `action stat.archetype who=<id> id=classic/warrior`, or set `"Archetype": "classic/mage"` in the character's JSON. To change a rule, ship the same entity id in a mod that depends on this pack, or patch it.

## If something goes wrong

1. **Skills do nothing in battle.** The game needs Battle Formulas for the `Formula=` values.
2. **The sheet shows no numbers for a character.** The character has no class and no stats of its own; give it an archetype.
