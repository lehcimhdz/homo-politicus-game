# Modding — Getting Started

Bienvenido al sistema de mods de Homo Politicus. Podés agregar **escenarios, líderes y eventos** sin tocar el código fuente del juego.

## Estructura de un mod

```
mods/
└── mi_mod/
    ├── mod.yaml              # Metadata obligatoria
    ├── scenarios/
    │   └── <id>.yaml         # Escenarios
    ├── leaders/
    │   └── <id>.yaml         # Líderes
    └── events/
        └── <thematic>.yaml   # Eventos
```

## mod.yaml mínimo

```yaml
id: mi_mod
name: "Mi escenario histórico"
author: "Tu nombre"
version: "1.0"
description: |
  Descripción del mod en uno o dos párrafos.
dependencies: core
```

## Quickstart: tu primer mod

1. Crear carpeta `~/Library/Application Support/HomoPoliticus/mods/mi_mod/` (macOS).
2. Crear `mod.yaml` con los campos de arriba.
3. Agregar un escenario:

```yaml
# mods/mi_mod/scenarios/mi_pais.yaml
id: mi_pais
version: 1
name_es: "Mi país en 2026"
start_year: 2026
difficulty: "★★★☆☆"
initial_country:
  welfare:
    population: 5000000
    literacy_rate: 0.95
  economy:
    gdp: 100000000000
    inflation: 0.04
  politics:
    popularity: 0.55
```

4. Iniciar el juego. El loader detecta el mod y lo lista en el menú.
5. Cargar tu escenario desde "Nueva partida" → "Histórico".

## Validación

El juego valida que:
- Cada mod tiene `id` único.
- Las dependencias existan.
- Los YAML parseen sin errores.

Si algo falla, ver `~/.config/HomoPoliticus/logs/mod_loader.log`.

## Siguientes pasos

- [Formato de escenarios completo](SCENARIO_FORMAT.md)
- [Formato de líderes](LEADER_FORMAT.md)
- [Formato de eventos](EVENT_FORMAT.md)
- [Publicar en Steam Workshop](WORKSHOP.md)
- [Troubleshooting](TROUBLESHOOTING.md)

## Comunidad

- Discord oficial: `#modding`.
- Subreddit: r/HomoPoliticus.
- Workshop: link directo desde el juego.
