# SPEC Fase 6 — Diseño de juego

## Objetivo
Convertir el motor de simulación en un **juego** con objetivos, retos, progresión, feedback y modos. Sin esto, lo que tenemos es solo una simulación abierta.

---

## 6.1 — Modos de juego

| Modo | Objetivo | Duración típica | Dificultad |
|---|---|---|---|
| **Sandbox** | Jugar libremente sin meta. | ∞ | Configurable |
| **Misiones** | Cumplir N objetivos específicos. | 20-50 turnos | 1-5 estrellas |
| **Histórico** | Recrear/cambiar un escenario real (ej. Cuba 1959). | 30-100 turnos | Fija por escenario |
| **Sucesor** | Heredás un país de otra partida acabada, ves consecuencias. | 30 turnos | Variable |
| **Iron Man** | Sandbox sin save/load, una partida única. | ∞ | Hardcore |

Cada modo expone parámetros iniciales:
- País inicial (preset o random).
- Líder (preset o custom).
- Año de arranque (afecta tech/eventos disponibles).
- Eventos extremos habilitados/deshabilitados.

---

## 6.2 — Tutorial (5 misiones guiadas, ~15 min)

### Misión 1: "Tu primer turno"
- **Objetivo:** pasar 3 turnos sin caída de popularidad >5%.
- **Enseña:** comando `next`, lectura del dashboard, lectura de popularidad.
- **Guion:** "Acabás de asumir. Mirá tu popularidad arriba a la derecha. Pasá 3 turnos haciendo nada. ¿Subió o bajó? ¿Por qué?"

### Misión 2: "Manejo fiscal"
- **Objetivo:** reducir inflación de 8% a 4% sin recesión.
- **Enseña:** `tax+`, `interest+`, trade-off ingresos/popularidad.
- **Guion:** "La inflación se disparó. Tenés tres formas de combatirla: subir impuestos, subir tasas, recortar gasto. Probá cada una y observá el efecto colateral."

### Misión 3: "Diplomacia con vecinos"
- **Objetivo:** mejorar relación con Easteria a +50.
- **Enseña:** comandos `improve_relations`, `trade_deal`.
- **Guion:** "Easteria tiene un reclamo territorial. Si llega a hostil sufrís presión. Mejorá la relación o firmá un tratado."

### Misión 4: "Crisis de escándalo"
- **Objetivo:** sobrevivir a un escándalo forzado sin caer bajo 30% popularidad.
- **Enseña:** `cover_up`, `scapegoat`, `apologize`, trade-offs.
- **Guion:** "Salió un escándalo. Tenés 3 opciones, cada una con costo distinto. Elegí sabiamente."

### Misión 5: "Decisión reactiva"
- **Objetivo:** resolver un coup_threat forzado preservando el régimen.
- **Enseña:** sistema de decisiones modales, presiones, fin de partida.
- **Guion:** "El alto mando se mueve. Te aparece un modal: leelo bien, elegí, viví con las consecuencias."

Tras la misión 5 → "Has aprendido lo básico. Ahora elegí un escenario histórico o sandbox para tu partida real."

---

## 6.3 — Sistema de objetivos y scoring

### Scoring por turno
- **Estabilidad**: popularidad × legitimidad. 0-100.
- **Crecimiento**: GDP delta vs turno anterior. -50 a +50.
- **Cohesión**: 100 - polarización × 100. 0-100.
- **Soft power**: prestigio diplomático × 100. 0-100.
- **Legado**: índice acumulado (educación + salud + infra + I+D). 0-100.

### Score final
`score = avg(estabilidad) × 0.3 + sum(crecimiento) × 0.2 + avg(cohesión) × 0.15 + soft_power_final × 0.15 + legado_final × 0.2`

Bonus:
- +20% si terminás por `TERM_COMPLETED` voluntario sin acciones autoritarias.
- -50% si terminás por `COUP_SUCCESS` o `EXILE`.
- +30% si Iron Man.

Ranking final: **Estadista (>85)** / **Líder competente (60-85)** / **Político pragmático (40-60)** / **Caudillo problemático (20-40)** / **Dictador fallido (<20)**.

---

## 6.4 — Logros (40+ achievements estilo Steam)

### Categoría: Supervivencia
1. **Primer año**: Sobrevive 12 turnos en cualquier modo.
2. **Década en el poder**: Sobrevive 40 turnos.
3. **Histórico**: Sobrevive 100 turnos.
4. **Iron man**: Terminá una partida Iron Man.

### Categoría: Económico
5. **Milagro económico**: GDP +50% sin recesión.
6. **Tigre asiático**: GDP +100% en 40 turnos.
7. **Inflación controlada**: Mantener inflación <3% durante 20 turnos.
8. **Default**: Llegar a credit rating D (sí, es un logro: aprendiste a fondo).
9. **Superávit**: Acumular SWF > $100M.

### Categoría: Político
10. **Reelección plebiscitaria**: Ganar elección con >70% del voto.
11. **Coalición arcoíris**: Mantener coalition_cohesion >0.8 durante 20 turnos.
12. **Constitucionalista**: Terminar con cero acciones autoritarias.
13. **Caudillo**: Acumular 10 acciones autoritarias.
14. **Plebiscito**: Convocar y ganar un referéndum.

### Categoría: Crisis
15. **Domador de crisis**: Resolver 5 decisiones críticas sin retroceder presiones.
16. **Sobreviviente nuclear**: Evitar nuclear strike teniendo nuclear_attack_prob > 0.5.
17. **Sin sangre**: Terminar guerra sin más de 1000 casualties.
18. **Paz duradera**: 30 turnos sin guerra ni guerra civil.
19. **Pacificador**: Resolver guerra civil mediante negociación.

### Categoría: Escándalos
20. **Teflón**: Sobrevive 5 escándalos sin caer bajo 40% popularidad.
21. **Houdini**: 3 cover_up exitosos consecutivos.
22. **Mártir**: Aplicar apologize en 5 escándalos.

### Categoría: Diplomática
23. **Soft power**: prestige > 0.9.
24. **Aliados regionales**: 3 vecinos con relación >+50.
25. **Anti-imperialista**: non_aligned_index > 0.8.
26. **Tratados**: Firmar 5 FTA.

### Categoría: Final
27. **Estadista**: Ranking final Estadista.
28. **Mártir popular**: Renunciar voluntariamente con popularidad > 70%.
29. **Coup d'état exitoso**: Llegar a EndCondition::COUP_SUCCESS (sí, contra vos).
30. **Aniquilación nuclear**: EndCondition::NUCLEAR_ANNIHILATION.
31. **Exilio dorado**: EndCondition::EXILE con SWF > $50M.
32. **Magnicidio**: EndCondition::ASSASSINATION.
33. **Revolución**: EndCondition::REVOLUTION sobreviviendo 5 turnos como acosado.

### Categoría: Escenarios históricos
34. **Argentina 1976**: Completar escenario sin coup.
35. **Cuba 1959**: Terminar con economía abierta.
36. **Venezuela 2013**: Evitar hiperinflación.
37. **Chile 1973**: Sobrevivir sin Pinochet (es decir, sin coup).
38. **Singapur 1965**: Llegar a GDP per cápita > $10K.

### Categoría: Meta
39. **Modder**: Cargar un mod custom.
40. **Speedrun**: Terminar partida en <10 turnos (game over rápido).

---

## 6.5 — Tabla de balance (constantes tuneables y exploits)

### Constantes a externalizar en `content/balance.yaml`:
```yaml
inflation:
  base_rate: 0.03
  popularity_penalty_threshold: 0.05  # +5% inflación = -X popularidad
  popularity_penalty_per_pct: 0.02
economy:
  recession_quarters_for_in_recession: 2
  recession_gdp_floor: 0.7   # No cae bajo 70% del pre-recession
politics:
  honeymoon_initial_turns: 4
  crisis_approval_floor: 0.2
  coup_attempts_decay: 0.05
scandals:
  cover_up_cost_multiplier: 10
  cover_up_judicial_penalty: 0.15
  apologize_severity_factor: 0.8
welfare:
  pandemic_base_severity: 0.3
  pandemic_duration_range: [4, 12]
```

### Exploits conocidos a parchear:
- **Print money loop**: `print+` ilimitado debería disparar hiperinflación >100% y bajar credit rating a D rápido. Actualmente sube inflación lentamente.
- **Apologize spam**: aplicar `apologize` cada turno debería tener fatiga (cooldown 3 turnos o efecto decreciente).
- **SWF spend infinito**: chequear que `swf_spend` no permita gastar más que el fondo.
- **Threaten spam**: amenazar al mismo vecino 5 veces seguidas debería romper relaciones a -1.0 y nada más.
- **Tax+ infinito**: cada uso debería tener efecto marginal decreciente (curva Laffer).

### Curva Laffer (a implementar):
```
tax_collection_multiplier(tax_level) = 1 + 0.8 × tax_level - 0.6 × tax_level²
```
Pico en `tax_level = 0.667`. Más alto reduce recaudación.

---

## 6.6 — Sistema de feedback legible

Cada acción del jugador debe mostrar:
1. **Mensaje narrativo** (1 línea).
2. **Deltas** (lista de variables que cambiaron y cuánto).
3. **Consecuencias secundarias** (¿qué riesgo subió?).

Ejemplo de output mejorado para `tax+`:
```
>> SUBES IMPUESTOS al 33%.
   Recaudación:    +10.0%  ($X → $Y)
   Popularidad:    -5.0%   (60% → 55%)
   Inflación:      +1.0%   (3% → 4%)
   Ideología:      gira hacia la izquierda (-2pt).
   Riesgo nuevo:   "Lobby empresarial protesta" (probabilidad +5%).
```

---

## 6.7 — Curva de dificultad

| País preset | Stabilidad inicial | Dificultad |
|---|---|---|
| **Estable** | pop 70%, GDP boom, sin guerra | ★☆☆☆☆ |
| **Frágil** | pop 50%, deuda alta, vecino hostil | ★★★☆☆ |
| **En crisis** | pop 30%, recesión, pandemia activa, escándalo heredado | ★★★★☆ |
| **Estado fallido** | pop 15%, guerra civil, hiperinflación | ★★★★★ |

---

## 6.8 — Validación
- Tutorial completable en <15 min por jugador nuevo.
- Score final consistente con jugabilidad observada.
- Logros no triviales pero alcanzables (test: 30% de jugadores debería completar 10+).
- Balance: ningún exploit permite popularidad >90% sostenida sin esfuerzo.
- Curva: jugador novato sobrevive Estable, experto disfruta Frágil, expertísimo elige En crisis.
