# Classic RPG Rules — 바로 쓸 수 있는 판타지 규칙 한 벌

Classic RPG Rules는 규칙 팩입니다. 코드 없이 데이터만 담고 있죠. 익숙한 판타지 세트 — 여섯 능력치, 체력과 마나, 세 직업, 세 장비 슬롯, 여덟 장비, 힘의 강장제, 전투 스킬 넷 — 을 게임에 줍니다. RPG Plus가 이 팩 위에 세워져 있고, 비주얼 노벨이라면 매력 판정용으로만 가져가도 됩니다.

규칙은 다른 모드를 통해 작동합니다. 수치 계산과 시트 표시는 Stats Engine, 장비를 착용 가능하게 하는 것은 Equipment, 가방 보관은 Items Base, 스킬 카드 보관은 Characters Engine, 스킬 공식을 전투에서 작동하게 하는 것은 Battle Formulas가 맡습니다. 이 팩은 *무엇이 있는지*만 말합니다.

## 게임 속에서

RPG Plus의 2레벨 도적, 철검 장착:

![RPG Plus의 2레벨 도적 캐릭터 시트: 힘 8, 민첩 12, 지능 5, 체력 6, 매력 9, 행운 9. 체력 34/34, 마나 14/14, 공격 18(+8), 방어구 3, 마력 7. 무기 슬롯에 철검(ATK +5).](assets/rogue-sheet.jpg)

모든 수치는 이 팩에서 옵니다. 도적의 민첩은 `8 + level * 2` = 12. 공격은 팩의 `str + level` = 8 + 2 = 10에 도적 특성 `floor(agi / 4)` = 3, 철검의 5를 더해 **18**이고, 이 중 **+8**이 보너스입니다. 힘 8은 도적의 `5 + level` = 7에 힘의 강장제 하나.

## 팩 안에 들어 있는 것

**능력치**(그룹 *Attributes*, 기본값 5): 힘 `str`, 민첩 `agi`, 지능 `int`, 체력 `vit`, 매력 `cha`, 행운 `luk`.

**전투 능력치**(그룹 *Combat*)는 능력치에서 계산됩니다:

```json
{ "Id": "hp",  "Formula": "12 + vit * 3 + level * 2", "Binds": "health" },
{ "Id": "mp",  "Formula": "int * 2 + level * 2", "Pool": true },
{ "Id": "atk", "Formula": "str + level", "Binds": "attack" },
{ "Id": "def", "Formula": "floor(vit / 2)", "Binds": "defence" },
{ "Id": "mag", "Formula": "int + level" }
```

체력, 공격, 방어는 전투 수치가 됩니다. 마나는 스킬이 소모하며 회복됩니다.

**직업** — 각각 레벨에 따라 능력치가 자라고, 특성을 하나 가지며, 스킬을 익힙니다:

- **전사** — 강하고 튼튼함(`str 7 + level * 2`, `vit 6 + level * 2`), 체력 +10%. 레벨 1에 Power strike, 레벨 3에 Guard break.
- **마법사** — `int 8 + level * 2`, 마나 +25%. 레벨 1에 Fireball, 레벨 2에 Heal.
- **도적** — `agi 8 + level * 2`, 매력과 행운 `7 + level`, 공격에 민첩의 4분의 1을 더함. 레벨 1에 Power strike, 레벨 2에 Guard break.

**슬롯**: 무기(검, 지팡이, 단검), 몸(갑옷, 로브), 장신구(부적, 반지).

**장비와 강장제**:

![팩의 아이템: 나무 검, 철검, 참나무 지팡이, 단검, 가죽 갑옷, 로브, 클로버 부적, 은반지, 힘의 강장제.](assets/gear.jpg)

- 나무 검: 공격 +2. 철검(전사, 도적): 공격 +5. 참나무 지팡이(마법사): 마력 +4, 마나 +5. 단검: 공격 +3, 민첩 +2.
- 가죽 갑옷: 방어 +2, 체력 +6. 로브: 마나 +6, 지능 +1.
- 클로버 부적: 행운 +3. 은반지: 매력 +2.
- 힘의 강장제: 힘 영구 +1.

**스킬** — 수치가 공식으로 정해지는 전투 행동입니다(`a`는 행동자, `b`는 대상):

![네 장의 스킬 카드: Power strike, Fireball, Heal, Guard break.](assets/skills.jpg)

```json
{ "Id": "classic/fireball", "Type": "ToEnemy",
  "Values": [ "RequiredPoint=1", "Cost=mp:4", "Pierce=1", "Formula=a.mag * 2 + rand(1, 6)" ] }
```

- Power strike: `a.atk * 1.5 + rand(0, a.str / 2)`.
- Fireball: 마나 4 소모, 방어구 무시, `a.mag * 2 + rand(1, 6)`.
- Heal: 마나 3 소모, 자신을 `a.mag * 2 + 4`만큼 회복.
- Guard break: 방어구 무시, `a.atk + b.defence * 2`, 35% 확률로 한 턴 기절.

## RPG Plus가 쓰는 방식

- 프롤로그의 첫 번째 대답이 영웅에게 세 직업 중 하나와 시작 무기를 줍니다.
- 마을 상인은 검, 단검, 지팡이, 갑옷을 팝니다(RPG Plus가 패치로 상점에 추가).
- 훈련장에서는 힘의 강장제를, 예언자에게서는 클로버 부적을 받습니다.
- 전투에서는 스킬이 Battle Formulas를 통해 작동합니다. 기본 전투 시스템과 카드 시스템 양쪽에서요.

## 내 게임에서 쓰기

게임 컬렉션에 `classic_rpg_pack`을 추가하세요. 의존 모드는 함께 로드됩니다. 그런 다음 `action stat.archetype who=<id> id=classic/warrior` 액션으로 캐릭터에게 직업을 주거나, 캐릭터 JSON에 `\"Archetype\": \"classic/mage\"`를 넣으세요. 규칙을 바꾸려면 이 팩에 의존하는 모드에서 같은 엔티티 id를 내보내거나 패치를 적용합니다.

## 문제가 생겼을 때

1. **스킬이 전투에서 아무 효과가 없음.** `Formula=` 값에는 Battle Formulas가 필요합니다.
2. **캐릭터의 시트에 수치가 안 보임.** 그 캐릭터에게는 직업도 고유 능력치도 없습니다. 원형을 지정해 주세요.
