# Tutorial — Guion de las 5 misiones

Cada misión está pensada para 2-4 minutos de juego efectivo + 30 seg de explicación. Total: ~15 min.

El tutorial debe poder saltarse en cualquier punto y, una vez completado, no volver a aparecer.

---

## Misión 1 — "Tu primer turno"

### Objetivo del jugador
Pasar 3 turnos consecutivos manteniendo popularidad ≥ 55%.

### Lo que se enseña
- Cómo lee el dashboard (popularidad arriba, sistemas en sidebar).
- Cómo pasar de turno (botón `next` o tecla N).
- Concepto de "honeymoon" post-elección.

### Script (pop-ups paso a paso)
**Pop-up 1 (T=0):**
> "Acabás de asumir como Presidente. Mirá tu **popularidad** arriba a la derecha. Empieza en 60% — el ‘luna de miel’ post-elección."

**Pop-up 2 (T=0, antes de pasar turno):**
> "Cada turno equivale a 3 meses. Para avanzar, presioná **Next**. ¿Listo?"

**Pop-up 3 (tras T=1):**
> "Tu popularidad cambió. Ningún número se mueve por casualidad — algún factor lo empujó. Mové el mouse sobre la cifra para ver qué lo causó."

**Pop-up 4 (tras T=3):**
> "¡Conseguiste sobrevivir 3 turnos sin caída! Ahora sabés leer el pulso del país. **Misión completada.**"

### Fallo
Si la popularidad cae bajo 55%, mensaje:
> "La luna de miel se terminó antes de tiempo. Probablemente fue un evento aleatorio. Reintentá."

### Reward
Logro `Primer año` desbloqueado si el jugador llega a turno 12 sin perder.

---

## Misión 2 — "Manejo fiscal"

### Setup forzado
Empieza con inflación al 8%, popularidad 55%, GDP en caída leve.

### Objetivo del jugador
Reducir inflación a ≤4% en 5 turnos sin entrar en recesión (≥2 trimestres de growth negativo).

### Lo que se enseña
- Comando `tax+` (sube recaudación, baja popularidad, baja inflación).
- Comando `interest+` (baja inflación, frena economía).
- Concepto de trade-off económico.

### Script
**Pop-up 1:**
> "La inflación se disparó al 8%. Si no actuás, va a seguir subiendo. Tenés dos palancas básicas: **impuestos** y **tasa de interés**."

**Pop-up 2:**
> "Probá `tax+` o presioná el botón **Subir impuestos**. Observá los tres números que cambian: recaudación ↑, popularidad ↓, inflación ↓."

**Pop-up 3:**
> "Ahora probá `interest+`. La inflación baja, pero la economía se frena. Vas a tener que combinar herramientas."

**Pop-up 4 (al cumplir objetivo):**
> "Inflación dominada sin recesión. Acabás de aprender política monetaria en 5 turnos. **Misión completada.**"

### Fallo
Si entra recesión:
> "Frenaste demasiado y el GDP cae. Reintentá ajustando con más sutileza."

---

## Misión 3 — "Diplomacia con vecinos"

### Setup forzado
Easteria (vecino del oeste) con relación -0.4 y `has_territorial_claim = true`.

### Objetivo del jugador
Llevar relaciones con Easteria a ≥ +0.5 en 4 turnos.

### Lo que se enseña
- Comandos `improve_relations`, `trade_deal`, `threaten`.
- Indicador de relación diplomática.
- Trade-off: mejorar relaciones cuesta diplomatic_prestige; amenazar sube popularidad pero deteriora.

### Script
**Pop-up 1:**
> "Easteria tiene un reclamo territorial activo. Si llega a hostil (-0.5), aparece un ultimátum. Tenés que mover relaciones antes."

**Pop-up 2:**
> "Probá `improve_relations 1` (Easteria es el vecino con índice 1). Mejora la relación pero gasta prestigio."

**Pop-up 3:**
> "Para un avance grande, firmá un `trade_deal 1`. Es más caro pero te trae al vecino al campo amistoso."

**Pop-up 4 (al cumplir objetivo):**
> "Easteria ahora es aliado. El reclamo territorial entró en pausa. **Misión completada.**"

---

## Misión 4 — "Crisis de escándalo"

### Setup forzado
Escándalo de corrupción con `severity = 0.7`. Popularidad inicial 50%.

### Objetivo del jugador
Sobrevivir 5 turnos sin caer bajo 35% popularidad.

### Lo que se enseña
- Comandos `cover_up`, `scapegoat`, `apologize`, `counter_narrative`.
- Cada uno con costo y riesgo distinto.
- Concepto de fatiga del escándalo.

### Script
**Pop-up 1:**
> "Salió un escándalo de corrupción. La prensa te ataca. Tu popularidad cae todos los turnos. **Tenés 4 estrategias.**"

**Pop-up 2:**
> "`cover_up`: encubrir. Funciona si tenés cover_up_probability alto, pero puede fallar y subir presión judicial."

**Pop-up 3:**
> "`scapegoat`: descargás el escándalo en un ministro. Funciona seguro, pero pierde 5% popularidad y polariza."

**Pop-up 4:**
> "`apologize`: disculpa pública. Reduce todos los escándalos a la mitad, pero costo inmediato de imagen."

**Pop-up 5 (al cumplir):**
> "Sobreviviste. La popularidad bajó pero estás vivo políticamente. **Misión completada.**"

---

## Misión 5 — "Decisión reactiva"

### Setup forzado
`military_pressure` empieza en 0.75 — disparará automáticamente la decisión `coup_threat`.

### Objetivo del jugador
Resolver el coup_threat sin perder el régimen (no game over).

### Lo que se enseña
- Modal de decisión bloqueante.
- Lectura de opciones y deltas esperados.
- Que algunas opciones son trampas (`cede_power` = game over inmediato).

### Script
**Pop-up 1:**
> "El alto mando se mueve. Una **DECISIÓN** apareció en pantalla. No podés avanzar hasta resolverla."

**Pop-up 2:**
> "Leé cada opción. Mové el mouse sobre los botones para ver qué cambia cada uno. Cuidado: `cede_power` significa que cedés el gobierno. Eso es **game over**."

**Pop-up 3:**
> "`purge_military` baja la presión drásticamente pero suma una acción autoritaria a tu legado. `negotiate_military` es más suave pero menos efectiva."

**Pop-up 4 (al resolver):**
> "Manejaste tu primera crisis institucional. El motor recordará tu decisión en futuros eventos."

### Cierre del tutorial
> "Has completado el tutorial. Ahora tenés tres caminos:
> - **Sandbox**: jugar libre sin meta.
> - **Misiones**: objetivos específicos por dificultad.
> - **Histórico**: revivir uno de los 5 escenarios reales.
>
> Buena suerte, presidente."

---

## Notas de implementación
- Los pop-ups son modales no bloqueantes (se pueden mover, no impiden ver el dashboard).
- Cada pop-up tiene botón "Saltar tutorial" en esquina superior derecha.
- El estado del tutorial se persiste en `~/Library/.../HomoPoliticus/profile.yaml` (no en savegame).
- Si el jugador completa el tutorial → flag global → no vuelve a aparecer.
