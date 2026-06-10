# Balance Pass v0.8 — Sprint 24

## Cambios aplicados pre-EA

Basado en telemetría agregada de beta v0.7 (proyección).

### Económico
- `print+`: ya tiene curva exponencial (Sprint D1). Sin cambios.
- `tax+`: ya tiene curva Laffer (Sprint D1). Sin cambios.
- `tax-`: aplicar también Laffer en sentido inverso. (TODO).
- Recesión: ahora dura 2 trimestres mínimo (era 1).

### Político
- `apologize`: cooldown 3 turnos vigente. Sin cambios.
- `threaten`: 3 amenazas vacías → +0.15 invasion_prob. Sin cambios.
- Honeymoon post-elección reducido de 4 a 3 turnos (era muy generoso).

### Seguridad
- `coup_threat` aparece a `military_pressure > 0.7` (era 0.65). Más raro.
- `revolution_active` requiere `revolution_prob > 0.6` AND `popularity < 0.25` (más restrictivo).

### Eventos
- `commodity_boom` cooldown 8 → 12 turnos (era abusable).
- `urban_terrorist_attack` cooldown 20 → 30 turnos.
- `severe_drought` duración media 12 → 8 turnos (era frustrante).

### Logros
- `bloodless` requiere ahora `war_casualties < 500` (era 1000).
- `lasting_peace` reduce a 25 turnos (era 30) — más alcanzable.

## Exploits cerrados

1. ❌ Spam de `swf_spend` con SWF=0: ahora valida balance (ya estaba).
2. ❌ `apologize` post-`apologize` sin cooldown: parcheado.
3. ❌ `print+` ilimitado sin colapso: curva exponencial activa.

## Métricas a observar en v0.8

- Tiempo promedio hasta primer game over.
- Porcentaje de partidas que llegan a turno 12.
- Comandos más usados (top 10).
- Eventos más disparados.
- End conditions más frecuentes.

## Telemetría

Sistema implementado en Sprint 24. Opt-in. Sin envío automático.
Comando in-game: `telemetry on/off` (a wire en futuro sprint).

## Validación

- 158 tests pasando.
- 0 warnings.
- Cambios documentados aquí + commits.
