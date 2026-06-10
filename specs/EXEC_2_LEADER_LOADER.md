# EXEC 2 — LeaderLoader

## Objetivo
Cargar YAML de líder y aplicar `starting_modifiers` al Country actual. Comando `leader <path>`.

## Alcance
- Reusar `MiniYaml`.
- Modificadores tipo `+0.20`, `-0.10`, `+30%`: parsear signo y valor.
- Aplicar a campos del Country (sumar al valor actual, no reemplazar).
- Validar bounds [0, 1] para variables normalizadas.
- Comando `leader <path>` en if/else legacy (igual que scenario).

## Campos soportados
Solo los que están en el YAML actual:
- politics.popularity, polarization_index, auth_dem_axis, economic_ideology, social_ideology,
  authoritarianism_prob, congressional_support, regime_legitimacy, judicial_independence,
  administrative_corruption, coalition_cohesion.
- security.press_freedom, diplomatic_prestige, soft_power_index, us_alignment, china_alignment,
  non_aligned_index, troop_morale.
- welfare.minority_protection, union_strength, torture_index.
- economy.central_bank_autonomy.

Campos no estándar del YAML (ej `oil_dependency_modifier`, `fiscal_expansion_modifier`,
`privatization_propensity`) se ignoran silenciosamente — no existen en el Country.

## Archivos a crear
- `include/LeaderLoader.hpp`
- `src/LeaderLoader.cpp`
- `tests/test_leader_loader.cpp`

## API
```cpp
namespace LeaderLoader {
    struct Metadata { std::string id; std::string name_es; std::string country_origin; };
    bool load(const std::string& path, Country& c, Metadata& meta);
}
```

## Parsing de modificadores
```yaml
starting_modifiers:
  popularity: +0.20      # → c.politics.popularity += 0.20
  press_freedom: -0.30   # → c.security.press_freedom -= 0.30
```

Reglas:
- Si valor empieza con `+` o `-`: delta aditivo.
- Si valor no tiene signo: tratarlo como delta aditivo igual (asumido `+`).
- Clamp a [0, 1] para variables que son índices.
- Clamp a [-1, +1] para `auth_dem_axis` (no, ese también es 0-1; revisar Country.hpp).

## Validación
- Cargar `peron.yaml` → popularidad sube +0.20, polarización +0.25, union_strength +0.30.
- Cargar `pinochet.yaml` → auth_dem_axis +0.40, press_freedom -0.40.
- Roundtrip: load → save → load mantiene los efectos aplicados.
- 4 tests unitarios.
