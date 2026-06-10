# SPEC Fase 14 — AI Advisor (asistente de gabinete vía LLM)

## Objetivo
Aprovechar la era de los LLMs para dar al jugador un **gabinete de asesores virtuales** que comenten su decisión, sugieran acciones, redacten discursos y analicen la situación. Diferenciador competitivo importante para 2026+.

---

## 14.1 — Concepto

El jugador puede invocar a 3-5 asesores virtuales:
- **Ministro de Hacienda** — análisis fiscal/monetario.
- **Jefe de Inteligencia** — análisis de presiones, amenazas, conspiraciones.
- **Vocero presidencial** — redacta discursos y comunicados.
- **Asesor político** — sugiere movidas tácticas.
- **Crítico opositor** — devuelve la perspectiva contraria.

Cada uno tiene **personalidad fija** y **acceso al estado actual del Country**.

---

## 14.2 — Arquitectura técnica

### Provider
- Claude API (Anthropic) como default (mejor para razonamiento político-económico).
- OpenAI GPT-4 como fallback.
- Modelo local (Llama 3.3 70B) para usuarios privacy-first.

### Prompt structure
Cada call envía:
```
[System prompt fijo del asesor + perspectiva]
[Estado actual del Country en YAML compacto]
[Historial: últimos 5 turnos resumidos]
[Pregunta del jugador]
```

### Caching
Estado del Country se cachea con prompt caching de Anthropic (5min TTL). Solo cambia delta por turno → reduce costos 80%+.

### Streaming
Respuesta streamed para sensación de "asesor pensando en vivo".

---

## 14.3 — Casos de uso

### 14.3.1 Análisis pre-decisión
"Ministro de Hacienda, ¿qué pasa si subo impuestos ahora?"
→ LLM lee el estado, predice 2-3 efectos, identifica riesgos, recomienda alternativa.

### 14.3.2 Redacción de discurso
"Vocero, redacta un anuncio justificando la represión de las protestas".
→ LLM produce 200 palabras en el tono del régimen actual.
→ Discurso afecta `narrative_reach` y `popularity` según calidad evaluada por otro LLM call.

### 14.3.3 Análisis de inteligencia
"Jefe de Inteligencia, ¿quién está conspirando?"
→ LLM lee `military_pressure`, `coup_d_etat_prob`, `congressional_pressure` y genera narrativa de inteligencia plausible. Puede revelar info útil o ser engañoso (humint_capability determina exactitud).

### 14.3.4 Crítica opositora
"Crítico, atacame".
→ LLM produce el tweet/op-ed que la oposición usaría. Útil para predecir narrativa adversa.

---

## 14.4 — Modelo de costos

### Para el desarrollador (Anthropic API)
- Input promedio: ~2000 tokens (estado + historial).
- Output: ~300 tokens (consejo).
- Con prompt caching: ~$0.001 por consulta.
- Jugador promedio: 10-20 consultas por partida = $0.01-0.02.
- 1000 jugadores × 10 partidas × $0.02 = $200/mes (manejable).

### Para el usuario
- **Tier gratis**: 5 consultas por partida.
- **Tier paid** ($4.99/mes): consultas ilimitadas.
- **Bring your own API key**: 0 costo para el desarrollador, ilimitado para el usuario.

---

## 14.5 — Riesgos y mitigaciones

### Riesgo 1: LLM dice barbaridades
- Mitigación: system prompt estricto que limite a comentar el estado simulado.
- Filtros post-hoc para contenido sensible.

### Riesgo 2: Costo escala fuera de control
- Mitigación: rate limit duro por usuario, prompt caching obligatorio.

### Riesgo 3: Latencia (3-5 seg por respuesta)
- Mitigación: streaming + animación "asesor pensando".

### Riesgo 4: Dependencia de proveedor externo
- Mitigación: abstracción `LLMProvider`, soporte local con Llama.

### Riesgo 5: Datos del jugador van al proveedor
- Mitigación: nunca enviar nombre real del jugador, solo gameplay state.

---

## 14.6 — UX en juego

```
+-----------------------------------------+
| Gabinete    |  [Ministro Hacienda] >    |
| Asesores    |  [Jefe Inteligencia]      |
|             |  [Vocero]                 |
|             |  [Asesor Político]        |
|             |  [Crítico Opositor]       |
+-------------+---------------------------+
| > Subir impuestos al 35% ahora?         |
|                                          |
| Ministro: "Tu deuda al 80% del PIB hace |
| que cada punto de tasa nueva sume al    |
| servicio de intereses. Antes de subir    |
| impuestos, considerá: 1) reestructurar   |
| deuda, 2) cerrar exenciones, 3) ..."     |
|                                          |
| [Aplicar] [Ignorar] [Preguntar a otro]   |
+-----------------------------------------+
```

---

## 14.7 — Por qué esto es valor diferencial

Ningún simulador político actual (2026) ofrece asesores LLM. Es el momento perfecto para liderar la categoría. Y el motor que ya tenés (Country struct con 200 variables) es **ideal** para alimentar un LLM con contexto rico.

## 14.8 — Validación
- 5 personajes de asesor implementados con personalidad distintiva.
- Cache hit rate >70% (prompt caching funciona).
- Costo promedio $0.02/partida.
- Latencia mediana <3 seg.
- Toggle ON/OFF para purists.
