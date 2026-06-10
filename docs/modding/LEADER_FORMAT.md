# Leader Format

## Estructura completa

```yaml
id: my_leader
version: 1
name_es: "Nombre"
name_en: "Name"
country_origin: "Pais"
years_active: "1990-2000"

bio_short_es: |
  ~100 palabras.

bio_long_es: |
  ~500 palabras con contexto histórico.

personality_traits: [populist, charismatic, military]

starting_modifiers:
  popularity: +0.20                  # delta aditivo
  press_freedom: -0.10
  diplomatic_prestige: +0.15
  # etc.

unique_actions:
  - id: "decree_special"
    name_es: "Decreto especial"
    effect: "popularity +0.10, debt +5%"

nemesis_traits:
  - description_es: "Tu carisma personal no se transfiere a tu sucesor"
    effect: "after you leave, popularity drops 20%"

historical_starting_year: 1990

trivia_es: |
  Curiosidades históricas para el manual.
```

## Modificadores soportados

Cualquier variable de `Country.hpp` puede ser modificada. Las más comunes:

```yaml
starting_modifiers:
  # Políticas
  popularity: +0.20
  polarization_index: +0.15
  auth_dem_axis: +0.20
  regime_legitimacy: +0.10
  economic_ideology: -0.20            # negativo = izquierda
  social_ideology: -0.15

  # Bienestar
  union_strength: +0.20
  minority_protection: +0.15
  torture_index: +0.10

  # Seguridad
  press_freedom: -0.20
  diplomatic_prestige: +0.20
  us_alignment: -0.30
  china_alignment: +0.15

  # Económico
  central_bank_autonomy: -0.10
```

## Formato de números

Siempre `+X` o `-X` para ser explícito sobre el signo. Sin signo se asume `+`.

## Validación

- `id` único.
- Modificadores en rango razonable: `[-1, +1]` para índices normalizados.
- Variables que no existen en Country.hpp son ignoradas con warning.

## Buenas prácticas

- Mantener `bio_short` ≤100 palabras (aparece en UI).
- `bio_long` para manual y diccionario.
- 3-5 `personality_traits` máximo.
- Modificadores entre `-0.30` y `+0.30` son razonables. Más allá rompe balance.
