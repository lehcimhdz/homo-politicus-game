# Event Format

## Estructura

```yaml
events:
  - id: my_event
    version: 1
    name_es: "Nombre legible"
    name_en: "Readable name"
    tags: [economic, opportunity]      # categorías
    trigger:
      expression: "country.economy.inflation > 0.5 AND country.politics.popularity < 0.4"
    probability_per_turn: 0.05
    cooldown_turns: 20
    flavor_text_es: |
      Texto narrativo que aparece cuando dispara el evento.
    effects:
      politics.popularity: "-0.05"
      economy.gdp: "*0.95"
      infra.drought_active: "true"
    decision_branches:
      - id: option_a
        label_es: "Opción A"
        effects: { popularity: "+0.05" }
      - id: option_b
        label_es: "Opción B"
        effects: { popularity: "-0.05", inflation: "+0.01" }
```

## Trigger expressions

Operadores soportados:
- Comparadores: `>`, `<`, `>=`, `<=`, `==`, `!=`
- Conectores: `AND`, `OR`
- Paths: `country.<system>.<field>` (resuelven a double)

Ejemplo:
```yaml
trigger:
  expression: "country.welfare.unemployment_rate > 0.15 AND country.politics.popularity < 0.4"
```

## Effects format

- `"*X"` multiplica el valor por X (ej: `"*0.95"`)
- `"+X"` suma X al valor (ej: `"+0.10"`)
- `"-X"` resta X del valor
- `"true"` / `"false"` para bools

Paths sin prefijo `country.`:

```yaml
effects:
  politics.popularity: "-0.05"        # OK
  economy.gdp: "*0.95"                # OK
  welfare.poverty_rate: "+0.10"       # OK
```

## Cooldown

`cooldown_turns: 20` significa que el evento no puede repetirse hasta 20 turnos después de su última activación.

## Decision branches

Si el evento debe presentar una decisión al jugador en lugar de aplicar efectos automáticamente, usar `decision_branches`. Cada opción tiene su propio set de efectos.

## Validación

- `id` único entre todos los eventos.
- `probability_per_turn` en `[0, 1]`.
- `trigger.expression` debe parsear sin error.
- Paths en effects deben existir en Country.hpp.

## Tags recomendados

- `economic`
- `political`
- `security`
- `welfare`
- `natural`
- `international`
- `crisis`
- `opportunity`
- `scandal`
- `tech`
