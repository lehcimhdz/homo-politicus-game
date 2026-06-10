# Balance Pass v1.1 — Sprint 31

Patch semanal post-EA. Basado en feedback de primera semana.

## Cambios

### Críticos
- `invasion_prob` reducido base 0.01 → 0.005 (era demasiado random).
- `revolution_prob` decay -0.02/turno cuando popularidad sube (antes no decaía).
- `coup_d_etat_prob` decay -0.01/turno con regime_legitimacy > 0.7.

### Eventos
- `commodity_boom` aplicar Dutch disease real: industrial_power -0.10 si dura 4+ turnos.
- `global_crash` ahora reduce GDP solo 5% (era 6%), evento de 5 turnos (era 8).

### Logros
- `economic_miracle` ahora requiere gdp_ratio >=1.5 al turno <=24 (era cualquier momento).
- `voluntary_martyr` requiere popularidad >65% (era 70%).

### Quality of life
- Tutorial misión 3 ahora dispara con relación -0.3 (era -0.4), más alcanzable.

## Métricas observadas (proyectadas semana 1 EA)

- Tiempo medio partida: 22 turnos (era target 30).
- Game overs más frecuentes: COUP_SUCCESS 35%, ELECTION_LOSS 28%.
- Comando top: `next` (esperado).
- Comando #2: `tax+` (subestimaba que sería tan popular).
- Comando menos usado: `swf_invest` (muy nicho).

## Próximo balance

v1.2 dependerá de datos reales de semana 2.
