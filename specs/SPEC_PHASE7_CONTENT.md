# SPEC Fase 7 — Contenido

## Objetivo
Generar la base de contenido que da vida al motor: escenarios prefabricados, líderes históricos, catálogo de eventos con flavor text, y localización ES/EN.

---

## 7.1 — Estructura YAML estándar

Todos los archivos en `content/` siguen este patrón:

```yaml
id: snake_case_unique          # Required
version: 1                     # Schema version
name_es: "Nombre en español"
name_en: "Name in English"
description_es: "Texto largo en ES"
description_en: "Text in EN"
# Bloques específicos por tipo (initial_state / triggers / effects)
```

Los loaders del motor (futuros) deserializan YAML → structs C++.

---

## 7.2 — Escenarios prefabricados (5)

Cada escenario en `content/scenarios/<id>.yaml`. Contiene:
- `initial_country`: estado completo del struct `Country` al iniciar.
- `objectives`: lista de objetivos por dificultad (bronce/plata/oro/diamante).
- `narrative_intro`: 200-400 palabras de contexto.
- `historical_notes`: link a fuentes, notas del diseñador.
- `available_actions_blacklist`: comandos vetados por contexto (ej. en 1965 no hay AI).
- `forced_events`: eventos que ocurren sí o sí en turnos específicos.

### Escenarios a crear:
1. **Argentina 1976** — golpe militar a Isabel Perón, junta militar, terrorismo de estado.
2. **Cuba 1959** — triunfo revolucionario, Fidel Castro, presión EEUU, alineación URSS.
3. **Venezuela 2013** — Maduro asume tras Chávez, inflación incipiente, polarización.
4. **Chile 1973** — Allende, hiperinflación, huelga camioneros, presión militar.
5. **Singapur 1965** — independencia forzada, Lee Kuan Yew, desarrollo desde cero.

---

## 7.3 — Líderes históricos (10)

Cada uno en `content/leaders/<id>.yaml`. Stats iniciales modifican el Country.

Campos:
- `bio_short`: 100 palabras.
- `bio_long`: 500 palabras.
- `personality_traits`: lista de tags ("populist", "technocrat", "military", "religious").
- `starting_modifiers`: deltas que se aplican al Country base.
- `unique_actions`: comandos especiales habilitados (ej. Castro tiene `nationalize_industries`).
- `nemesis_traits`: rasgos que ponen al líder en problemas estructurales.

### Líderes:
1. **Juan Domingo Perón** (Argentina) — populista, alta popularidad inicial, polarización extrema.
2. **Margaret Thatcher** (UK) — neoliberal, antisindical, baja popularidad inicial.
3. **Nelson Mandela** (Sudáfrica) — alta legitimidad moral, sin recursos.
4. **Salvador Allende** (Chile) — socialista, oposición militar alta.
5. **Augusto Pinochet** (Chile) — dictador, autoritarianism inicial alto.
6. **Lee Kuan Yew** (Singapur) — tecnócrata, autoritarianism medio, anti-corrupción.
7. **Fidel Castro** (Cuba) — revolucionario, sanctions probability alta.
8. **Hugo Chávez** (Venezuela) — populista petrolero, polarización alta.
9. **Angela Merkel** (Alemania) — pragmática, baja polarización, popularidad estable.
10. **Lula da Silva** (Brasil) — populista moderado, mediador.

---

## 7.4 — Catálogo de eventos (30 mínimo)

Cada uno en `content/events/<id>.yaml`. Reemplaza el sistema actual de prob/severity sueltos.

Campos:
- `id`, `name_es`, `name_en`.
- `trigger`: condición de activación (expression-like).
- `probability_per_turn`: prob base.
- `flavor_text_es`, `flavor_text_en`: 100-300 palabras narrativos.
- `effects`: deltas al Country.
- `decision_branches`: opciones del jugador (similar a decision queue actual).
- `cooldown_turns`: no se repite hasta N turnos.
- `tags`: categorías (`economic`, `political`, `security`, `welfare`, `crisis`).

### Eventos prioritarios (30):

**Económicos (8):**
1. Boom de commodities.
2. Crash bursátil global.
3. Fuga de capitales.
4. Hiperinflación.
5. Huelga general industrial.
6. Quiebra de banco sistémico.
7. Recesión tecnológica.
8. Crisis cambiaria.

**Políticos (8):**
9. Renuncia de ministro estrella.
10. Defección legislativa masiva.
11. Atentado a líder opositor (el jugador es sospechoso).
12. Filtración de audios privados.
13. Disputa con corte suprema.
14. Pacto de gobernadores en contra.
15. Movilización pro-gobierno espontánea.
16. Crisis migratoria interna por desempleo.

**Seguridad / Internacional (7):**
17. Atentado terrorista urbano.
18. Captura de narcotraficante mayor.
19. Frontera caliente con vecino.
20. Sanciones por DD.HH.
21. Espionaje extranjero descubierto.
22. Visita de superpotencia (alineación forzada).
23. Cumbre regional fracasada.

**Bienestar / Naturales (7):**
24. Pandemia regional.
25. Sequía severa multi-año.
26. Huracán de categoría 5.
27. Brote epidémico urbano.
28. Hambruna por falla de cosecha.
29. Crisis migratoria por refugiados.
30. Terremoto.

---

## 7.5 — Localización ES/EN

`content/locales/es.yaml` y `en.yaml`. Diccionarios `clave: traducción`.

Cobertura mínima:
- Todos los nombres de comandos del help.
- Todos los textos del dashboard.
- Todos los mensajes de end-game.
- Eventos y decisiones (referenciados desde sus YAML).

Política: ES es la versión canónica del autor. EN se mantiene en paridad pero puede ir un commit atrás.

---

## 7.6 — Validación
1. 5 archivos en `content/scenarios/`.
2. 10 archivos en `content/leaders/`.
3. 30 archivos en `content/events/`.
4. 2 archivos en `content/locales/`.
5. Cada YAML válido (parsea con `python3 -c "import yaml; yaml.safe_load(open('<file>'))"`).
6. Cada uno tiene los campos `id`, `version`, `name_es`, `name_en`.
7. Sin colisiones de `id` entre archivos.
