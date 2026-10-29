# Classic RPG Rules —— 现成的奇幻规则集

Classic RPG Rules 是一套规则包：里面没有代码，只有数据。它为游戏提供熟悉的奇幻配置——六项属性、生命与法力、三个职业、三个装备栏位、八件装备、一瓶力量药剂和四个战斗技能。RPG Plus 就构建在它之上；视觉小说也可以只为魅力检定而引入它。

这些规则要靠其他模组才能生效。Stats Engine 负责计算数值并展示面板，Equipment 让装备可以穿戴，Items Base 把装备收进背包，Characters Engine 存放技能卡，Battle Formulas 让技能公式在战斗中运转。这个包只负责声明*有什么*。

## 游戏中的表现

RPG Plus 中一名手持铁剑的 2 级盗贼：

![RPG Plus 中 2 级盗贼的角色面板：力量 8、敏捷 12、智力 5、体力 6、魅力 9、幸运 9；生命 34/34、法力 14/14、攻击 18（+8）、护甲 3、魔力 7；武器栏位中是铁剑（ATK +5）。](assets/rogue-sheet.jpg)

每个数值都来自这个规则包。盗贼的敏捷是 `8 + level * 2` = 12。攻击用的是规则包公式 `str + level` = 8 + 2 = 10，加上盗贼特性 `floor(agi / 4)` = 3，再加铁剑的 5——合计 **18**，其中 **+8** 是加成。力量 8 则是盗贼的 `5 + level` = 7，再加一瓶力量药剂。

## 规则包里有什么

**属性**（分组 *Attributes*，默认值 5）：力量 `str`、敏捷 `agi`、智力 `int`、体力 `vit`、魅力 `cha`、幸运 `luk`。

**战斗属性**（分组 *Combat*）由属性推算而来：

```json
{ "Id": "hp",  "Formula": "12 + vit * 3 + level * 2", "Binds": "health" },
{ "Id": "mp",  "Formula": "int * 2 + level * 2", "Pool": true },
{ "Id": "atk", "Formula": "str + level", "Binds": "attack" },
{ "Id": "def", "Formula": "floor(vit / 2)", "Binds": "defence" },
{ "Id": "mag", "Formula": "int + level" }
```

生命、攻击和防御会成为战斗数值；法力由技能消耗并可恢复。

**职业**——每个职业的属性都随等级成长，拥有一项特性并逐步习得技能：

- **战士**——强壮耐打（`str 7 + level * 2`、`vit 6 + level * 2`），生命 +10%；1 级习得 Power strike，3 级习得 Guard break。
- **法师**——`int 8 + level * 2`，法力 +25%；1 级习得 Fireball，2 级习得 Heal。
- **盗贼**——`agi 8 + level * 2`，魅力和幸运 `7 + level`，攻击加敏捷的四分之一；1 级习得 Power strike，2 级习得 Guard break。

**栏位**：武器（剑、法杖、匕首）、身体（护甲、长袍）、饰品（护符、戒指）。

**装备与药剂**：

![规则包的物品：木剑、铁剑、橡木法杖、匕首、皮甲、长袍、四叶草护符、银戒指、力量药剂。](assets/gear.jpg)

- 木剑：攻击 +2。铁剑（战士、盗贼）：攻击 +5。橡木法杖（法师）：魔力 +4，法力 +5。匕首：攻击 +3，敏捷 +2。
- 皮甲：防御 +2，生命 +6。长袍：法力 +6，智力 +1。
- 四叶草护符：幸运 +3。银戒指：魅力 +2。
- 力量药剂：力量永久 +1。

**技能**——数值由公式决定的战斗动作（`a` 是行动者，`b` 是目标）：

![四张技能卡：Power strike、Fireball、Heal、Guard break。](assets/skills.jpg)

```json
{ "Id": "classic/fireball", "Type": "ToEnemy",
  "Values": [ "RequiredPoint=1", "Cost=mp:4", "Pierce=1", "Formula=a.mag * 2 + rand(1, 6)" ] }
```

- Power strike: `a.atk * 1.5 + rand(0, a.str / 2)`.
- Fireball：消耗 4 点法力，无视护甲，`a.mag * 2 + rand(1, 6)`。
- Heal：消耗 3 点法力，为自身回复 `a.mag * 2 + 4`。
- Guard break：无视护甲，`a.atk + b.defence * 2`，35% 几率眩晕一回合。

## RPG Plus 如何使用它

- 序章的第一个选择会赋予英雄三个职业之一及其初始武器。
- 村庄商人出售两把剑、匕首、法杖和护甲（RPG Plus 通过补丁把它们加进商店）。
- 训练场会给一瓶力量药剂；先知则赠予四叶草护符。
- 战斗通过 Battle Formulas 使用这些技能，默认战斗系统和卡牌系统皆可。

## 在你的游戏中使用

把 `classic_rpg_pack` 加入游戏的合集，它依赖的模组会随之加载。然后用动作 `action stat.archetype who=<id> id=classic/warrior` 给角色指定职业，或在角色 JSON 里设置 `\"Archetype\": \"classic/mage\"`。想修改某条规则，可以在依赖本包的模组中发布同 id 的实体，或打补丁。

## 出问题怎么办

1. **技能在战斗中毫无效果。** 游戏需要 Battle Formulas 来处理 `Formula=` 值。
2. **某角色的面板上没有数值。** 该角色没有职业也没有自有属性；给它指定一个原型即可。
