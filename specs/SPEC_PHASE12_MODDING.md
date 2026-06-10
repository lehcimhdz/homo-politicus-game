# SPEC Fase 12 — Modding y extensibilidad

## Objetivo
Permitir que la comunidad agregue escenarios, líderes, eventos sin tocar código fuente. Modelo Paradox / Cities Skylines.

---

## 12.1 — Carpeta de mods

`~/Library/Application Support/HomoPoliticus/mods/<mod_name>/` (macOS).
Cada mod es un directorio con:
- `mod.yaml`: metadata (nombre, autor, versión, dependencias).
- `content/scenarios/`, `content/leaders/`, `content/events/`: mismo formato que core.
- `content/locales/`: traducciones extras.
- `assets/`: imágenes, sonidos (futuro).

---

## 12.2 — Loader de mods

Al inicio:
1. Carga `content/` del core.
2. Lista `mods/` activados (en `~/.config/HomoPoliticus/mods_enabled.yaml`).
3. Carga cada mod en orden, **overrides** core si comparten `id`.
4. Reporta conflictos en log.

---

## 12.3 — Hooks de eventos custom

`mod.yaml` puede declarar eventos que reaccionan a:
- Turno N específico.
- Condición sobre Country state.
- Otro evento dispara.

Sintaxis YAML para condiciones:
```yaml
trigger:
  expression: "country.economy.inflation > 0.5 AND country.politics.popularity < 0.3"
```

El expression parser es simple: AND, OR, NOT, comparadores, paths a structs.

---

## 12.4 — Steam Workshop integration (post-launch)

- Subscribe a mods desde el juego.
- Sincronización automática con Workshop.
- Rating de mods.
- Reportar mod problemático.

---

## 12.5 — Modding API documentación

Crear `docs/modding/`:
- `getting_started.md`
- `scenario_format.md`
- `event_format.md`
- `leader_format.md`
- `locales.md`
- `troubleshooting.md`

---

## 12.6 — Validación
- Mod ejemplo "Argentina 2001" cargable desde directorio externo.
- Conflictos de ID reportados claramente.
- Mods desactivables uno por uno.
- Hooks de eventos funcionan con expresiones complejas.
