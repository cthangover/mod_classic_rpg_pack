# Classic RPG Rules — un set de reglas de fantasía listo para usar

Classic RPG Rules es un paquete de reglas: no contiene código, solo datos. Da a un juego el conjunto de fantasía de siempre — seis atributos, salud y maná, tres clases, tres ranuras de equipo, ocho piezas de equipo, un tónico de fuerza y cuatro habilidades de combate. RPG Plus está construido sobre él; una novela visual podría tomarlo solo para las pruebas de carisma.

Las reglas funcionan a través de otros mods. Stats Engine calcula los números y muestra la hoja, Equipment hace que el equipo se pueda llevar, Items Base lo guarda en la bolsa, Characters Engine guarda las cartas de habilidades, y Battle Formulas hace que las fórmulas de las habilidades funcionen en combate. Este paquete solo dice *qué* hay.

## En el juego

Un pícaro de nivel 2 en RPG Plus con la espada de hierro:

![La hoja de personaje de un Pícaro de nivel 2 en RPG Plus: Fuerza 8, Agilidad 12, Inteligencia 5, Vitalidad 6, Carisma 9, Suerte 9; Salud 34/34, Maná 14/14, Ataque 18 (+8), Armadura 3, Magia 7; la Espada de hierro (ATK +5) en la ranura de Arma.](assets/rogue-sheet.jpg)

Cada número viene de este paquete. La agilidad del pícaro es `8 + level * 2` = 12. El ataque es la fórmula del paquete `str + level` = 8 + 2 = 10, más el rasgo del pícaro `floor(agi / 4)` = 3, más los 5 de la espada de hierro — **18**, de los cuales **+8** son bonos. La fuerza 8 es el `5 + level` del pícaro = 7 y un tónico de fuerza.

## Qué contiene el paquete

**Atributos** (grupo *Attributes*, por defecto 5): Fuerza `str`, Agilidad `agi`, Inteligencia `int`, Vitalidad `vit`, Carisma `cha`, Suerte `luk`.

**Estadísticas de combate** (grupo *Combat*), calculadas a partir de los atributos:

```json
{ "Id": "hp",  "Formula": "12 + vit * 3 + level * 2", "Binds": "health" },
{ "Id": "mp",  "Formula": "int * 2 + level * 2", "Pool": true },
{ "Id": "atk", "Formula": "str + level", "Binds": "attack" },
{ "Id": "def", "Formula": "floor(vit / 2)", "Binds": "defence" },
{ "Id": "mag", "Formula": "int + level" }
```

Salud, ataque y defensa se convierten en los números de combate; el maná lo gastan las habilidades y se recupera.

**Clases** — cada una hace crecer sus atributos con el nivel, tiene un rasgo y aprende habilidades:

- **Guerrero** — fuerte y resistente (`str 7 + level * 2`, `vit 6 + level * 2`), +10% de salud; Power strike en el nivel 1, Guard break en el 3.
- **Mago** — `int 8 + level * 2`, +25% de maná; Fireball en el nivel 1, Heal en el 2.
- **Pícaro** — `agi 8 + level * 2`, carisma y suerte `7 + level`, ataque + un cuarto de la agilidad; Power strike en el nivel 1, Guard break en el 2.

**Ranuras**: Arma (espada, bastón, daga), Cuerpo (armadura, túnica), Accesorio (amuleto, anillo).

**Equipo y un tónico**:

![Los objetos del paquete: espada de madera, espada de hierro, bastón de roble, daga, armadura de cuero, túnica, amuleto de trébol, anillo de plata, tónico de fuerza.](assets/gear.jpg)

- Espada de madera: ataque +2. Espada de hierro (guerrero, pícaro): ataque +5. Bastón de roble (mago): magia +4, maná +5. Daga: ataque +3, agilidad +2.
- Armadura de cuero: defensa +2, salud +6. Túnica: maná +6, inteligencia +1.
- Amuleto de trébol: suerte +3. Anillo de plata: carisma +2.
- Tónico de fuerza: fuerza +1 para siempre.

**Habilidades** — acciones de combate cuyos números son fórmulas (`a` es quien actúa, `b` el objetivo):

![Las cuatro cartas de habilidades: Power strike, Fireball, Heal, Guard break.](assets/skills.jpg)

```json
{ "Id": "classic/fireball", "Type": "ToEnemy",
  "Values": [ "RequiredPoint=1", "Cost=mp:4", "Pierce=1", "Formula=a.mag * 2 + rand(1, 6)" ] }
```

- Power strike: `a.atk * 1.5 + rand(0, a.str / 2)`.
- Fireball: cuesta 4 de maná, ignora la armadura, `a.mag * 2 + rand(1, 6)`.
- Heal: cuesta 3 de maná, cura al propio personaje `a.mag * 2 + 4`.
- Guard break: ignora la armadura, `a.atk + b.defence * 2`, 35% de probabilidad de aturdir un turno.

## Cómo lo usa RPG Plus

- La primera respuesta del prólogo da al héroe una de las tres clases y su arma inicial.
- El mercader de la aldea vende las espadas, la daga, el bastón y la armadura (RPG Plus los añade a la tienda con un parche).
- El patio de entrenamiento da un tónico de fuerza; la vidente, el amuleto de trébol.
- Los combates usan las habilidades mediante Battle Formulas, tanto en el sistema de combate predeterminado como en el de cartas.

## Cómo usarlo en tu juego

Añade `classic_rpg_pack` a la colección del juego; los mods de los que depende se cargan con él. Luego dale una clase a un personaje con `action stat.archetype who=<id> id=classic/warrior`, o pon `\"Archetype\": \"classic/mage\"` en el JSON del personaje. Para cambiar una regla, envía la misma entidad con el mismo id en un mod que dependa de este paquete, o aplícale un parche.

## Si algo sale mal

1. **Las habilidades no hacen nada en combate.** Para los valores `Formula=` el juego necesita Battle Formulas.
2. **La hoja no muestra números de un personaje.** El personaje no tiene clase ni estadísticas propias; dale un arquetipo.
