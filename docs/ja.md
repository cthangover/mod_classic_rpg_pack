# Classic RPG Rules —— すぐ使えるファンタジールール一式

Classic RPG Rules はルールパックです。コードは含まず、データだけ。おなじみのファンタジーのセット——六つの能力値、HP と MP、三つの職業、三つの装備スロット、八つの装備、力の強壮剤、そして四つのバトルスキル——をゲームに与えます。RPG Plus はこれの上に築かれており、ビジュアルノベルならカリスマ判定のためだけに取り入れることもできます。

ルールは他のModを通して働きます。数値の計算とシート表示は Stats Engine、装備可能にするのは Equipment、かばんへの収納は Items Base、スキルカードの管理は Characters Engine、そしてスキルの式をバトルで機能させるのは Battle Formulas。このパックは*何があるか*を述べるだけです。

## ゲームの中で

RPG Plus のレベル 2 盗賊、鉄の剣を装備：

![RPG Plus のレベル 2 盗賊のキャラクターシート：筋力 8、敏捷 12、知力 5、体力 6、カリスマ 9、運 9。HP 34/34、MP 14/14、攻撃 18（+8）、防具 3、魔力 7。武器スロットには鉄の剣（ATK +5）。](assets/rogue-sheet.jpg)

どの数値もこのパックから来ています。盗賊の敏捷は `8 + level * 2` = 12。攻撃はパックの `str + level` = 8 + 2 = 10 に、盗賊の特性 `floor(agi / 4)` = 3、鉄の剣の 5 を加えて **18**。うち **+8** がボーナスです。筋力 8 は盗賊の `5 + level` = 7 と力の強壮剤一つ。

## パックの中身

**能力値**（グループ *Attributes*、初期値 5）：筋力 `str`、敏捷 `agi`、知力 `int`、体力 `vit`、カリスマ `cha`、運 `luk`。

**戦闘ステータス**（グループ *Combat*）は能力値から計算されます：

```json
{ "Id": "hp",  "Formula": "12 + vit * 3 + level * 2", "Binds": "health" },
{ "Id": "mp",  "Formula": "int * 2 + level * 2", "Pool": true },
{ "Id": "atk", "Formula": "str + level", "Binds": "attack" },
{ "Id": "def", "Formula": "floor(vit / 2)", "Binds": "defence" },
{ "Id": "mag", "Formula": "int + level" }
```

体力・攻撃・防御は戦闘値になります。MP はスキルが消費し、回復します。

**職業**——それぞれレベルとともに能力値が伸び、特性をひとつ持ち、スキルを習得します：

- **戦士**——強くて頑丈（`str 7 + level * 2`、`vit 6 + level * 2`）、HP +10%。レベル 1 で Power strike、レベル 3 で Guard break。
- **魔法使い**——`int 8 + level * 2`、MP +25%。レベル 1 で Fireball、レベル 2 で Heal。
- **盗賊**——`agi 8 + level * 2`、カリスマと運は `7 + level`、攻撃に敏捷の 4 分の 1 を加算。レベル 1 で Power strike、レベル 2 で Guard break。

**スロット**：武器（剣、杖、短剣）、体（鎧、ローブ）、アクセサリー（お守り、指輪）。

**装備と強壮剤**：

![パックのアイテム：木の剣、鉄の剣、樫の杖、短剣、革の鎧、ローブ、クローバーのお守り、銀の指輪、力の強壮剤。](assets/gear.jpg)

- 木の剣：攻撃 +2。鉄の剣（戦士・盗賊）：攻撃 +5。樫の杖（魔法使い）：魔力 +4、MP +5。短剣：攻撃 +3、敏捷 +2。
- 革の鎧：防御 +2、HP +6。ローブ：MP +6、知力 +1。
- クローバーのお守り：運 +3。銀の指輪：カリスマ +2。
- 力の強壮剤：筋力 +1（恒久）。

**スキル**——数値を式で決めるバトルアクションです（`a` は行動者、`b` は対象）：

![4枚のスキルカード：Power strike、Fireball、Heal、Guard break。](assets/skills.jpg)

```json
{ "Id": "classic/fireball", "Type": "ToEnemy",
  "Values": [ "RequiredPoint=1", "Cost=mp:4", "Pierce=1", "Formula=a.mag * 2 + rand(1, 6)" ] }
```

- Power strike: `a.atk * 1.5 + rand(0, a.str / 2)`.
- Fireball：MP 4 消費、防具を無視、`a.mag * 2 + rand(1, 6)`。
- Heal：MP 3 消費、自分を `a.mag * 2 + 4` だけ回復。
- Guard break：防具を無視、`a.atk + b.defence * 2`、35% の確率で一ターン気絶させる。

## RPG Plus での使い方

- プロローグの最初の答えで、英雄は三つの職業のどれかとその初期武器を得ます。
- 村の商人は剣二本、短剣、杖、鎧を売っています（RPG Plus はパッチで店に追加）。
- 訓練場は力の強壮剤を、預言者はクローバーのお守りをくれます。
- バトルでは、スキルは Battle Formulas を通して動きます。標準のバトルシステムでもカード式でも。

## 自分のゲームで使う

`classic_rpg_pack` をゲームのコレクションに加えてください。依存するModは一緒に読み込まれます。次に、`action stat.archetype who=<id> id=classic/warrior` でキャラクターに職業を与えるか、キャラクターの JSON に `\"Archetype\": \"classic/mage\"` を設定します。ルールを変えたいときは、このパックに依存するModで同じエンティティ id を送るか、パッチを当てます。

## うまくいかないときは

1. **スキルが戦闘で何もしない。** `Formula=` 値には Battle Formulas が必要です。
2. **キャラクターのシートに数値が出ない。** そのキャラクターには職業も固有のステータスもありません。アーキタイプを与えてください。
