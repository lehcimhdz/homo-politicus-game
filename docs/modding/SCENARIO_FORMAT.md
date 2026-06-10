# Scenario Format

## Estructura completa

```yaml
id: unique_snake_case_id              # OBLIGATORIO, único en todo el juego
version: 1                            # OBLIGATORIO
name_es: "Nombre en español"
name_en: "Name in English"
start_year: 1976                      # Año de inicio
start_turn: 0                         # Casi siempre 0
difficulty: "★★★★☆"                  # 1-5 estrellas

description_es: |
  Texto largo describiendo el momento histórico.
description_en: |
  Same in English.

narrative_intro_es: |
  200-400 palabras de contexto narrativo que el jugador lee al inicio.

initial_country:
  welfare:
    population: 25800000
    poverty_rate: 0.35
    literacy_rate: 0.93
    health_coverage: 0.55
    unemployment_rate: 0.04
    # ... resto de variables welfare
  economy:
    gdp: 90000000000
    inflation: 4.44
    growth_rate: -0.012
    in_recession: true
    # ... resto de variables economy
  politics:
    popularity: 0.45
    auth_dem_axis: 0.85
    # ... etc
  security:
    troop_morale: 0.85
    diplomatic_prestige: 0.30
  infra:
    pollution_prob: 0.30
    co2_emissions: 80000000

forced_events:
  - turn: 1
    event_id: "ley_22068"
    description_es: "Intervención de las universidades"
  - turn: 8
    event_id: "mundial_1978"
    description_es: "Argentina organiza el Mundial"

available_actions_blacklist:
  - swf_save                          # Comandos que no aplican al contexto
  - hedge_prices

objectives:
  bronce:
    description_es: "Objetivo fácil"
    condition: "turn >= 12 AND end_condition == NONE"
  plata:
    description_es: "Objetivo medio"
  oro:
    description_es: "Objetivo difícil"
  diamante:
    description_es: "Objetivo casi imposible"

historical_notes_es: |
  Notas para el jugador después de terminar.

sources:
  - "Libro A (1990)"
  - "Documental B (2010)"
```

## Variables disponibles

Ver `include/Country.hpp` para la lista completa (~200 variables). Las más usadas:

### Welfare
- `population`, `poverty_rate`, `literacy_rate`, `health_coverage`
- `unemployment_rate`, `general_strike_prob`, `radicalism_prob`
- `freedom_of_expression`, `minority_protection`, `un_score`
- `torture_index`, `forced_disappearances`

### Economy
- `gdp`, `inflation`, `growth_rate`, `in_recession`
- `international_reserves`, `interest_rate`, `central_bank_autonomy`
- `debt_to_gdp_ratio`, `credit_rating` (0-10)

### Politics
- `popularity`, `regime_legitimacy`, `auth_dem_axis`
- `economic_ideology`, `social_ideology`, `polarization_index`
- `congressional_support`, `judicial_independence`
- `coup_d_etat_prob`, `revolution_prob`

### Security
- `diplomatic_prestige`, `troop_morale`, `military_spending_gdp`
- `us_alignment`, `china_alignment`, `non_aligned_index`

### Infrastructure
- `pollution_prob`, `co2_emissions`, `internet_coverage`
- `road_connectivity`, `renewables_percentage`

## Validación común

- Probabilidades en `[0, 1]`.
- Índices normalizados en `[0, 1]` salvo `diplomatic_relations` que es `[-1, +1]`.
- Population como entero, GDP como double en USD.
