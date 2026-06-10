# MASTER DEVELOPMENT SPEC — Homo Politicus

> Documento ejecutivo y técnico de **52 semanas** para llevar Homo Politicus desde
> prototipo terminal a producto comercial en Steam.
>
> Este documento se ejecuta sprint por sprint. Cada sprint dura **una semana**.
> El proyecto entero se calcula en **52 semanas = 12 meses calendario** con dedicación
> de ~30 horas semanales.
>
> Última revisión: junio 2026. Versión: 1.0.

---

## Tabla de contenidos

0. [Cómo usar este documento](#0-cómo-usar-este-documento)
1. [Snapshot del baseline](#1-snapshot-del-baseline-junio-2026)
2. [Filosofía y principios técnicos](#2-filosofía-y-principios-técnicos)
3. [Convenciones de código y commits](#3-convenciones-de-código-y-commits)
4. [HITO 1 — Closed Alpha (semanas 1-8)](#4-hito-1--closed-alpha-semanas-1-8)
5. [HITO 2 — Open Beta UI (semanas 9-20)](#5-hito-2--open-beta-ui-semanas-9-20)
6. [HITO 3 — Early Access (semanas 21-28)](#6-hito-3--early-access-semanas-21-28)
7. [HITO 4 — v1.0 Release (semanas 29-52)](#7-hito-4--v10-release-semanas-29-52)
8. [Plan de testing exhaustivo](#8-plan-de-testing-exhaustivo)
9. [Plan de QA y bug bash](#9-plan-de-qa-y-bug-bash)
10. [Plan de marketing semanal](#10-plan-de-marketing-semanal)
11. [Plan de contenido (escenarios, líderes, eventos)](#11-plan-de-contenido)
12. [Plan de comunidad](#12-plan-de-comunidad)
13. [Plan post-launch (DLCs y ediciones)](#13-plan-post-launch)
14. [Salud del proyecto (anti-burnout)](#14-salud-del-proyecto)
15. [Apéndices: templates y checklists](#15-apéndices)

---

## 0. Cómo usar este documento

### Para el ejecutor (vos o cualquier dev)
1. Lee el sprint actual al principio de cada lunes.
2. Cumplí los entregables. Si no se puede, anotá el bloqueo y mová a "siguiente
   sprint disponible".
3. Cerrá el sprint el viernes con commit final y nota de retrospectiva.
4. Saltá a marketing/comunidad si el sprint técnico se completa antes.

### Para alguien que se suma al proyecto
1. Lee secciones 1, 2, 3 (contexto).
2. Lee el HITO actual completo.
3. Pregunta antes de modificar este documento.

### Para revisión externa
Cada sección tiene **criterios de aceptación medibles**. Si un sprint no los cumple,
se retrabaja antes de avanzar.

### Reglas inviolables
- Nunca skipear un sprint sin retrospectiva escrita.
- Nunca lanzar a Early Access si Hito 3 no pasa todas sus checklist.
- Nunca prometer públicamente un hito sin tener el anterior cerrado.

---

## 1. Snapshot del baseline (junio 2026)

### Engine (`homo-politicus`)
```
11.435 líneas C++17
15 archivos .cpp + .hpp
62 tests pasando (100%)
0 warnings de compilación
187 commits
```

### Módulos extraídos
- `Game` — loop central
- `Country` — datos (200+ variables)
- `EndCondition` — enum
- `Persistence` — save/load
- `GameOverChecker` — evaluador
- `DecisionSystem` — cola de decisiones
- `FeedbackBuilder` — helper de output
- `MiniYaml` — parser YAML propio
- `ScenarioLoader` — carga escenarios
- `LeaderLoader` — carga líderes
- `AchievementTracker` — 15 logros
- `ExpressionEvaluator` — parser de expresiones
- `EventLoader` — 5 eventos scripted
- `Advisor` — 5 asesores canned
- `EventManager` — legacy
- `TestFramework` — header-only

### Content (`homo-politicus-game`)
```
56 archivos
9 commits
8 escenarios YAML
15 líderes YAML
30 eventos YAML en 4 archivos
2 locales (ES/EN)
40 logros catalogados
5 documentos de diseño
11 specs Phase 5-15 + 5 specs EXEC
```

### Lo que NO existe todavía
- UI gráfica.
- Audio.
- Onboarding implementado (solo escrito).
- Loaders YAML completos (eventos, locales).
- 25 de 40 logros.
- 80 comandos legacy todavía en `if/else`.
- Save/load para >30 variables.
- AI Advisor con LLM real.
- Steam integration.
- Marketing assets.
- Comunidad establecida.

---

## 2. Filosofía y principios técnicos

### 2.1 Diseño de código

1. **Simplicidad antes que abstracción**. Tres bloques `if/else` ganan a una jerarquía
   de clases con virtual. Solo abstraer cuando hay 3+ casos reales.
2. **Sin features prematuras**. Si nadie las pidió, no se implementan.
3. **Tests son contratos**. Si pasaron antes y ya no pasan, hay regresión real.
4. **Errores de boundary, no internos**. Validar en `cin >>`, no en cada método interno.
5. **Sin manejo de errores cosmético**. Nada de try/catch que solo recapture el mismo error.
6. **Hot path libre de allocations**. `update()` no llama a `new` ni a `string` por turno.
7. **`std::cout` es para feedback al jugador**, no para debugging. Para debugging usar
   `std::cerr` con flag `--debug`.

### 2.2 Diseño de juego

1. **Cada acción tiene costo y consecuencia**. Sin acciones "gratis" salvo `next` y `exit`.
2. **Sin save scumming aprobado**. Save/load existen, Iron Man es modo aparte.
3. **Decisiones son irrevocables dentro de un turno**. No hay "deshacer".
4. **Información asimétrica intencional**. El jugador no ve todas las variables — solo
   las relevantes a su rol.
5. **Eventos son narrativos antes que numéricos**. Flavor text es first-class.

### 2.3 Diseño de UX

1. **Toda métrica tiene tooltip con su fórmula y movers**.
2. **Toda decisión muestra deltas esperados al hover**.
3. **Cero ventanas modales que no se puedan dismiss**.
4. **El estado del juego siempre visible** (no esconder dashboard durante diálogos).
5. **Dark mode default**, light mode opcional.

### 2.4 Diseño de contenido

1. **Honestidad histórica**. Sin embellecimiento, sin negacionismo.
2. **Bibliografía citada**. Toda figura histórica tiene 2+ fuentes referenciadas.
3. **Sin endorsement político**. El motor muestra costos de cada ideología, no
   prescribe ninguna.
4. **Localización paralela**. ES y EN se desarrollan juntos, no secuencial.

---

## 3. Convenciones de código y commits

### 3.1 Commits
- **Mensaje single-line en presente, imperativo**.
- Ejemplos buenos:
  - `Add AchievementTracker with 15 unlockable achievements and 8 tests`
  - `Fix expression evaluator returning false for negative numbers`
  - `Refactor Game.cpp by extracting WelfareSystem to separate module`
- Ejemplos malos:
  - `Updates` (vago)
  - `fix bug` (vago)
  - `Added stuff and refactored a few things` (múltiples cambios)
- **Sin Co-Authored-By** salvo PR de colaborador humano explícito.
- **Sin emojis** en commits.

### 3.2 Código C++

```cpp
// Headers: include guards en mayúscula con _HPP final
#ifndef ACHIEVEMENT_TRACKER_HPP
#define ACHIEVEMENT_TRACKER_HPP

// Includes en orden:
// 1. Propio header (en .cpp)
// 2. Headers del proyecto en orden alfabético
// 3. Headers estándar

// Naming
class CamelCase { };
void snake_case_method();
double snake_case_variable;
const int kConstant = 42;  // o CamelCase si es enum class

// Imports siempre completos
std::vector<std::string> v;  // no `using namespace std;`

#endif
```

### 3.3 Tests
- Un `TEST_CASE` por escenario discreto.
- Naming: `<modulo>_<escenario>_<expectativa>`. Ej: `expr_simple_gt_returns_true`.
- Setup mínimo, sin fixtures globales (header-only no los soporta).
- Cada bug nuevo descubierto → primero test que lo reproduce, después fix.

### 3.4 Branches y PRs
- `main` siempre verde (compila + tests).
- Features grandes en branch `feat/<nombre>`.
- PR a `main` con descripción + tests + smoke test manual.
- Self-review obligatorio antes de merge solo.

---

## 4. HITO 1 — Closed Alpha (semanas 1-8)

**Objetivo:** versión terminal completamente pulida, validada con 10-20 testers.
**Versión target:** 0.5-alpha
**Sin UI gráfica todavía.**

### Sprint 1 (semana 1) — EventLoader completo

**Objetivo del sprint:** que los 30 eventos YAML reales se carguen y disparen.

**Entregables:**

1. **MiniYaml extendido** para soportar:
   - Listas con `-` indentadas.
   - Sub-bloques `effects:` con key:value.
   - Sub-bloques `decision_branches:` con anidación de 2 niveles.

2. **Archivos a modificar:**
   - `include/MiniYaml.hpp`: API extendida con `KVList` además de `KV`.
   - `src/MiniYaml.cpp`: nuevo parser de listas.
   - `tests/test_miniyaml.cpp`: 8 tests nuevos cubriendo casos extendidos.

3. **EventLoader extendido:**
   - `loadAll(const std::string& dir)` que recorre directorio y parsea cada YAML.
   - Maneja archivos multi-evento (los thematic files).
   - Errores de carga van a log, no a crash.

4. **Smoke test in-game:**
   - Comando `events_load <dir>` carga los 4 archivos de `content/events/`.
   - Comando `events_list` muestra los 30 cargados.
   - `next` dispara probabilísticamente los que cumplen trigger.

**Tests de aceptación:**
- `MiniYaml` parsea correctamente `commodity_boom` con su `decision_branches`.
- `EventLoader::loadAll` retorna ≥30 eventos cargados.
- Smoke test: con país en hiperinflación, el evento `hyperinflation` se dispara en
  ≤5 turnos con prob 1.0.
- 0 warnings nuevos.
- `make test` pasa 70+ tests.

**Tiempo estimado:** 30 horas.

---

### Sprint 2 (semana 2) — Localización funcional

**Objetivo:** strings del motor cargados desde `content/locales/<lang>.yaml`.

**Entregables:**

1. **Nuevo módulo `Localization`:**
   - `include/Localization.hpp`
   - `src/Localization.cpp`
   - API: `Localization::tr("key.subkey")` retorna string en idioma activo.

2. **Path de claves estilo:**
   - `commands.tax_up.name`
   - `end_conditions.coup_success`
   - `ui.welcome`

3. **Modificar archivos del engine:**
   - Game.cpp: cambiar todos los `std::cout << "GAME OVER..."` a
     `std::cout << Localization::tr("ui.game_over_header")`.
   - Mínimo 50 strings localizados.

4. **Comando `language <es|en>`** cambia idioma activo en runtime.

5. **Tests:**
   - `tests/test_localization.cpp` con 6 tests.
   - Cubre: clave faltante retorna la clave (fallback), idioma cambiado en runtime
     refleja inmediato, ES y EN cargan sin error.

**Tests de aceptación:**
- `language en` y todo el output cambia a inglés.
- `language es` vuelve a español.
- Si una clave no existe, no crashea: muestra `[missing: ui.foo]`.
- `make test` pasa 76+ tests.

**Tiempo estimado:** 25 horas.

---

### Sprint 3 (semana 3) — Refactor del legacy if/else

**Objetivo:** migrar los 80 comandos restantes al dispatch map.

**Entregables:**

1. **Migración en lotes de 20 comandos por día.**
2. **Eliminar bloques `else if` correspondientes** después de migrar.
3. **Tamaño objetivo:** `processEvents()` < 200 líneas (hoy ~2000).

**Estrategia:**
- Día 1: 20 comandos fiscales/welfare.
- Día 2: 20 comandos políticos.
- Día 3: 20 comandos económicos avanzados.
- Día 4: 20 comandos de relaciones/diplomacia.
- Día 5: Refactor del manejo de comandos con argumentos (scenario, leader,
  improve_relations, threaten).

**Comandos con argumentos:**
- Crear `commandHandlersArg<std::string>` aparte.
- Procesar `cin >> arg` dentro del handler.

**Tests de aceptación:**
- 100% de comandos pre-existentes siguen funcionando idénticos.
- `processEvents()` < 200 líneas.
- `make test` pasa.
- Smoke test manual de 20 comandos al azar.

**Tiempo estimado:** 40 horas (sprint más pesado del Hito 1).

---

### Sprint 4 (semana 4) — Save/load extendido

**Objetivo:** persistir TODAS las variables del Country, no solo 30.

**Entregables:**

1. **Persistence v2:**
   - Schema versionado: header `schema_version: 2`.
   - Persistir 200+ variables del Country.
   - Persistir cola de pendingDecisions.
   - Persistir scriptedEvents con su last_fired_turn.
   - Persistir AchievementTracker.

2. **Formato:**
   - Sigue siendo key=value (no JSON, mantener simple).
   - Una sección por sistema: `[welfare]`, `[economy]`, etc.
   - Comentarios `#` permitidos.

3. **Migrador:**
   - Si carga save con `schema_version: 1` → migra agregando defaults.
   - Falla limpia si schema desconocido.

4. **Checksum:**
   - Última línea: `checksum=<sha256-de-las-anteriores>`.
   - Si checksum no coincide al cargar → warning + carga igual.

5. **Slots de save:**
   - `save 1` guarda en `savegame_1.txt`.
   - `save quick` guarda en `savegame_quick.txt`.
   - `load 1` carga slot 1.

**Tests de aceptación:**
- Round-trip de los 200+ campos preserva valor exacto.
- Save v1 carga correctamente en v2 (migración).
- 5 slots funcionan independientes.
- `make test` pasa 90+ tests.

**Tiempo estimado:** 30 horas.

---

### Sprint 5 (semana 5) — Logros restantes (15 a 40)

**Objetivo:** implementar los 25 logros con state tracking.

**Entregables:**

1. **History tracking en `AchievementTracker`:**
   - `popularity_never_below: double` — min popularidad histórica.
   - `consecutive_successful_coverups: int`.
   - `scandals_endured: int`.
   - `apologize_count: int`.
   - etc.

2. **Hooks en update():**
   - Después de cada acción, actualizar el state tracking.
   - Después de cada evento, evaluar logros que dependen del estado actual.

3. **Logros a implementar:**
   - `economic_miracle`, `asian_tiger` (no sólo end-state, growth over time).
   - `inflation_tamed` con duración real.
   - `rainbow_coalition` con duración.
   - `crisis_tamer` (5 decisiones críticas sin retroceder presiones).
   - `nuclear_survivor`, `bloodless`, `lasting_peace`, `peacemaker`.
   - `teflon`, `houdini`, `martyr`.
   - `regional_allies`, `non_aligned`, `trade_master`.
   - `landslide_reelection`, `referendum_winner`.
   - `voluntary_martyr`, `golden_exile`.
   - 5 escenarios históricos específicos.

4. **Tests:**
   - 25 tests, uno por logro nuevo.

**Tests de aceptación:**
- Los 40 logros del catálogo son desbloqueables.
- `make test` pasa 115+ tests.
- Smoke: jugar partida que dispare 5+ logros y verificar.

**Tiempo estimado:** 30 horas.

---

### Sprint 6 (semana 6) — Tutorial scripted

**Objetivo:** implementar las 5 misiones del tutorial como secuencias jugables.

**Entregables:**

1. **Nuevo módulo `Tutorial`:**
   - `include/Tutorial.hpp`
   - `src/Tutorial.cpp`
   - Estado: `currentMission: int`, `currentStep: int`, `completed: bool`.

2. **Lógica:**
   - Al iniciar el juego, si `~/.config/HomoPoliticus/profile.yaml` no tiene
     `tutorial_completed: true` → entra modo tutorial automáticamente.
   - Cada misión es una secuencia de pop-ups (impresos en terminal) + setup forzado
     del Country + objetivo evaluado por turno.

3. **5 misiones según `tutorial_script.md`:**
   - M1: Primer turno (3 turnos sin caída).
   - M2: Manejo fiscal (inflación 8% a 4%).
   - M3: Diplomacia (relación Easteria +50).
   - M4: Crisis de escándalo (sobrevivir).
   - M5: Decisión reactiva (coup_threat).

4. **Persistencia:**
   - Completar tutorial → flag global persistido.
   - `tutorial skip` para skipearlo.
   - `tutorial reset` para repetirlo.

5. **Tests:**
   - 5 tests, uno por misión, simulando completarla.

**Tests de aceptación:**
- Usuario nuevo arrancando el juego ve M1 inmediatamente.
- Cada misión es completable en <3 minutos.
- Skip funciona.
- `make test` pasa 120+ tests.

**Tiempo estimado:** 35 horas.

---

### Sprint 7 (semana 7) — Feedback consistente con FeedbackBuilder

**Objetivo:** todos los comandos del jugador usan FeedbackBuilder.

**Entregables:**

1. **Refactorizar 80 comandos** a usar FeedbackBuilder:
   - Capturar estado pre-acción.
   - Aplicar acción.
   - Capturar estado post-acción.
   - Build con deltas, riesgos, predicción.

2. **Predicciones contextuales:**
   - Cada acción tiene 1-2 líneas de predicción basadas en estado actual.
   - Ej: `tax+` con inflación alta → "próximo turno: la inflación seguirá subiendo si
     no acompañás con interest+".

3. **Tests:**
   - Verificar que 10 comandos al azar emiten ≥3 deltas.
   - Verificar que comandos con riesgo emiten ≥1 riesgo.

**Tests de aceptación:**
- Smoke test de 10 comandos muestra output detallado consistente.
- Ningún comando emite solo 1 línea (excepto exit/next/save/load).
- `make test` pasa.

**Tiempo estimado:** 35 horas.

---

### Sprint 8 (semana 8) — Closed Alpha launch + retrospectiva Hito 1

**Objetivo:** publicar el binario terminal a 10-20 testers, recibir feedback.

**Entregables:**

1. **README de jugadores** en raíz de `homo-politicus`:
   - Cómo compilar (3 plataformas).
   - Cómo arrancar.
   - 10 comandos esenciales con ejemplo.
   - Link a Discord para feedback.

2. **Build artifacts:**
   - Binarios compilados para macOS, Linux, Windows (cross-compile o WSL).
   - Hosted en GitHub Releases como `v0.5-alpha`.
   - Checksums SHA256.

3. **Discord abierto:**
   - Canal `#bug-reports`.
   - Canal `#feedback-general`.
   - Canal `#balance-discussion`.
   - Canal `#anuncios`.

4. **Formulario de feedback:**
   - Google Form con 10 preguntas:
     - Tiempo jugado.
     - Misión más confusa del tutorial.
     - Comando más usado / menos usado.
     - Escenario favorito.
     - 3 cosas que más le gustaron.
     - 3 cosas que más le frustraron.
     - ¿Recomendaría el juego? (NPS 0-10).
     - ¿Pagaría por la versión con UI? (sí/no/quizá).
     - ¿Cuánto? (rango).
     - Comentarios libres.

5. **Outreach:**
   - Email a 30 contactos personales (amigos, exalumnos, foros indie).
   - Post en r/IndieDev, r/strategy (con permiso de mods).
   - Post en LinkedIn.

6. **Retrospectiva Hito 1:**
   - Documento `RETRO_HITO_1.md` con:
     - Sprints completados a tiempo / no.
     - Bugs encontrados.
     - Lecciones aprendidas.
     - Ajustes al Hito 2 si correspondiera.

**Métrica de éxito Hito 1:**
- 10 testers completan al menos 1 partida.
- NPS ≥ 30.
- ≥50% dice "pagaría por UI".

**Si la métrica de éxito NO se cumple:**
- Pausa de 1 semana para reflexión.
- Decisión: pivotear, continuar, archivar.

**Tiempo estimado sprint 8:** 25 horas (mucho de marketing/comunidad).

---

## 5. HITO 2 — Open Beta UI (semanas 9-20)

**Objetivo:** UI gráfica funcional jugable por usuario no técnico.
**Versión target:** 0.7-beta
**Engine UI:** SFML 2.6 + Dear ImGui.

### Sprint 9 (semana 9) — Setup SFML + ImGui

**Entregables:**

1. **Dependencias:**
   - Documentar `brew install sfml` (macOS) y equivalentes Linux/Windows.
   - Submodule de `imgui` + `imgui-sfml` en `external/`.

2. **Makefile extendido:**
   - Target `make ui` que compila con SFML linkado.
   - Flag `SFML_PREFIX` configurable.

3. **Hello World gráfico:**
   - `src/ui/main_ui.cpp` que abre ventana 1280x800.
   - Muestra texto "Homo Politicus" centrado.
   - Cierra con click en X.

4. **Tests:**
   - Test de smoke: el binario UI arranca y cierra sin crash.

**Tests de aceptación:**
- `make ui` produce binario `HomoPoliticusUI`.
- Ventana se abre en 3 OS.
- 60 fps sin contenido.

**Tiempo estimado:** 25 horas.

---

### Sprint 10 (semana 10) — Layout principal y TopBar

**Entregables:**

1. **Estructura de pantalla:**
   - TopBar (height 60px).
   - SidebarLeft (width 200px).
   - MainPanel (flexible).
   - SidebarRight (width 250px).
   - BottomBar (height 100px).

2. **TopBar muestra:**
   - Turno actual.
   - Popularidad con barra de progreso.
   - GDP en formato $XM.
   - Inflación %.
   - Indicador de alerta si hay decisión pendiente.

3. **Bridge engine ↔ UI:**
   - `UIBridge` clase que expone `Country` actual a la UI sin copiar.
   - `UIBridge::tick()` ejecuta turn del engine.
   - `UIBridge::sendCommand(string)` traduce a comando del dispatch map.

**Tests de aceptación:**
- Layout responde a redimensionar ventana.
- TopBar refleja estado del Country.
- Bridge no tiene memory leaks (validar con leaks tool).

**Tiempo estimado:** 30 horas.

---

### Sprint 11 (semana 11) — Dashboard tab (6 cards)

**Entregables:**

1. **Card 1: Popularidad gauge + sparkline.**
2. **Card 2: Economía con 3 líneas (GDP, inflación, desempleo).**
3. **Card 3: Presiones como 5 barras horizontales.**
4. **Card 4: Escándalos lista.**
5. **Card 5: Sistemas país gauges agregados.**
6. **Card 6: Vecinos chips.**

**Cada card:**
- 320x200 px.
- Tooltip al hover con detalle.
- Click → navega al panel detallado de ese sistema.

**Histórico para sparklines:**
- `History` struct: `vector<double> popularity_history;` etc.
- Limpiar a 100 puntos máximo.

**Tests de aceptación:**
- 6 cards renderizan en grid 2x3.
- Sparkline muestra últimos N turnos.
- Tooltips funcionan.

**Tiempo estimado:** 35 horas.

---

### Sprint 12 (semana 12) — Mapa tab

**Entregables:**

1. **Mapa SVG-inspirado:**
   - País central como polígono (forma genérica si no hay assets).
   - 3-5 vecinos como polígonos adyacentes.
   - Color según estabilidad (verde a rojo).

2. **Líneas comerciales:**
   - Entre país y vecinos.
   - Grosor proporcional a `trade_volume`.
   - Animación de puntos viajando (3-5 puntos por línea, loop).

3. **Iconos de eventos:**
   - ☠ pandemia.
   - ⚔ guerra.
   - 🌪 tormenta.
   - 💰 boom económico.
   - Solo el último activo se muestra.

4. **Click en vecino:**
   - Modal con stats bilaterales.
   - Botones: improve_relations, worsen_relations, trade_deal, threaten.

**Tests de aceptación:**
- Mapa renderiza sin assets externos (vector art).
- Animaciones 60fps.
- Click en vecino abre modal correcto.

**Tiempo estimado:** 35 horas.

---

### Sprint 13 (semana 13) — Action tab (familias de comandos)

**Entregables:**

1. **10 sub-tabs por familia:**
   - Fiscal, Recursos, SWF, Comercio, Ambiental, Monetario, Política, Diplomacia,
     Vecinos, Escándalos.

2. **Cada comando como botón:**
   - Icono (genérico o de FontAwesome).
   - Nombre legible (no `tax+` sino "Subir impuestos").
   - Tooltip con efectos esperados (de la tabla de balance).
   - Costo visible si aplica.
   - Disabled si no aplica.

3. **Confirmación opcional:**
   - Acciones con costo alto piden confirmación.
   - Toggle "no preguntar más por esta acción".

**Tests de aceptación:**
- 80+ comandos visibles y clickables.
- Comandos disabled correctamente según estado.
- Tooltip con efectos pre-acción.

**Tiempo estimado:** 35 horas.

---

### Sprint 14 (semana 14) — Modal de decisión

**Entregables:**

1. **Overlay modal:**
   - Centrado, 600x400 px.
   - Fondo semi-transparente.
   - Bloquea interacción con resto.

2. **Contenido:**
   - Título grande.
   - Texto descriptivo.
   - Imagen contextual (genérica si no hay arte).
   - 3-4 botones de opciones.

3. **Tooltips por opción:**
   - Hover sobre opción muestra deltas previstos.
   - "Skip" como botón discreto.

4. **Animación de entrada:**
   - Fade in 200ms.
   - Slight bounce.

**Tests de aceptación:**
- Modal aparece cuando hay decisión pendiente.
- Botones disparan decision handlers correctos.
- Skip pone decisión al final de cola.

**Tiempo estimado:** 25 horas.

---

### Sprint 15 (semana 15) — Audio system

**Entregables:**

1. **Música ambiente:**
   - 3 tracks loopeables (calmo, tensión, crisis).
   - Selección automática según estado.
   - Crossfade entre tracks.

2. **SFX por evento:**
   - 10 efectos: turn_advance, achievement_unlock, decision_pending, game_over,
     scandal_revealed, war_declared, victory, crash, applause, warning.

3. **Settings:**
   - Volumen música (slider 0-100).
   - Volumen SFX (slider 0-100).
   - Mute global.

4. **Fuentes de audio:**
   - Música: contratar compositor freelance ($300-500) o usar Creative Commons.
   - SFX: bibliotecas gratuitas (freesound.org con atribución).

**Tests de aceptación:**
- Música suena al arrancar.
- SFX disparan en eventos correctos.
- Mute funciona.
- No hay clipping ni distorsión.

**Tiempo estimado:** 30 horas (incluye 5h de selección/curación audio).

---

### Sprint 16 (semana 16) — Pantalla principal (main menu)

**Entregables:**

1. **Menu principal:**
   - Background art (genérico inicial).
   - Logo "HOMO POLITICUS".
   - Botones grandes:
     - **Nuevo juego** (selección modo).
     - **Continuar** (carga último save).
     - **Cargar partida** (lista slots).
     - **Logros** (lista 40).
     - **Configuración** (settings).
     - **Salir**.

2. **Selección de modo:**
   - 5 cards: Sandbox, Misiones, Histórico, Sucesor (locked si no jugaste), Iron Man.
   - Selección de país, año, líder.

3. **Configuración:**
   - Idioma (ES/EN).
   - Audio (música/SFX volume).
   - Gameplay (velocidad, eventos extremos on/off).

**Tests de aceptación:**
- Menu navegable solo con mouse.
- Logros muestra progreso.
- Settings persisten entre sesiones.

**Tiempo estimado:** 30 horas.

---

### Sprint 17 (semana 17) — Pantalla de game over

**Entregables:**

1. **Animación de fade a negro.**
2. **Pantalla final con:**
   - Condición de fin (gigante).
   - Resumen de la partida (turnos, popularidad media, decisiones autoritarias).
   - Logros desbloqueados.
   - Score final + ranking.
   - Botones: Nueva partida, Continuar como sucesor (si aplica), Menú principal.

3. **Animaciones específicas por condición:**
   - Coup: flash rojo.
   - Nuclear: mushroom cloud.
   - Election loss: confeti irónico.

4. **Compartir:**
   - "Compartir resultado en X/Twitter" con captura auto-generada.

**Tests de aceptación:**
- 9 condiciones de fin renderizan correcto.
- Score correcto.
- Share funciona.

**Tiempo estimado:** 25 horas.

---

### Sprint 18 (semana 18) — Localización UI

**Entregables:**

1. **Todas las strings UI desde `Localization::tr()`.**
2. **Switch idioma en runtime:**
   - Setting → cambia inmediatamente.
   - No requiere restart.

3. **Fuentes con soporte completo ES y EN:**
   - Cargar fuente con caracteres acentuados (latin1 minimum, prefer UTF-8).

4. **Testing con hablantes nativos:**
   - 1 nativo ES (vos).
   - 1 nativo EN (contactar).
   - Revisión de strings con contexto.

**Tests de aceptación:**
- 100% UI traducida en ambos idiomas.
- Switch en runtime sin glitch.
- Tildes y eñes renderizan.

**Tiempo estimado:** 30 horas.

---

### Sprint 19 (semana 19) — Tutorial UI

**Entregables:**

1. **Tutorial reimplementado** sobre la UI:
   - Pop-ups gráficos (no más impresos en terminal).
   - Highlight de elementos (ej: flecha apuntando a Popularidad).
   - "Saltar tutorial" botón siempre visible.

2. **5 misiones igual que sprint 6 pero gráficas.**

3. **Tooltips contextuales:**
   - Primer click en cualquier elemento del dashboard → tooltip extra explicativo.

**Tests de aceptación:**
- Tutorial completable en 15 min por nuevo jugador.
- Skip funciona.
- Highlights apuntan correctamente.

**Tiempo estimado:** 35 horas.

---

### Sprint 20 (semana 20) — Open Beta launch + retrospectiva Hito 2

**Entregables:**

1. **Build UI compilada** para Win/macOS/Linux.

2. **Página Steam:**
   - Aplicación Steamworks ($100 fee).
   - Tags, descripción, capsule, header.
   - Wishlist abierta.
   - "Coming Soon" badge.

3. **Trailer 60s:**
   - Tipos posibles:
     - Concepto: 30 escenas rápidas mostrando gameplay.
     - Narrativo: cuenta una historia (un líder asume, escala, cae).
   - Música licenciada o original.

4. **10 screenshots calidad Steam (1920x1080).**

5. **Outreach beta:**
   - 100 invitaciones a Discord.
   - Coverage a 5 streamers de strategy.
   - Beta access vía Discord rol.

6. **Retrospectiva Hito 2:**
   - `RETRO_HITO_2.md`.

**Métrica de éxito Hito 2:**
- ≥5.000 wishlists en Steam.
- ≥100 jugadores en beta.
- ≥75% retention día 7 en beta.

**Tiempo estimado:** 40 horas.

---

## 6. HITO 3 — Early Access (semanas 21-28)

**Objetivo:** lanzar pagado en Steam.
**Versión target:** 1.0-ea
**Precio:** $14.99 USD.

### Sprint 21 (semana 21) — Steamworks integration

**Entregables:**

1. **Steamworks SDK integrado** (1.59+).
2. **Achievements:**
   - 40 logros sincronizan con Steam.
3. **Cloud saves:**
   - `savegame_*.txt` sincronizados a Steam Cloud.
4. **Rich Presence:**
   - "Jugando como Mandela, turno 12".
5. **Stats globales:**
   - Tracking: turnos jugados, escándalos, países más jugados.

**Tests de aceptación:**
- Achievements desbloquean en Steam Console.
- Cloud sync entre 2 máquinas.
- Rich Presence visible en perfil Steam.

**Tiempo estimado:** 30 horas.

---

### Sprint 22 (semana 22) — AI Advisor LLM real

**Entregables:**

1. **Anthropic API integration:**
   - Cliente HTTP con libcurl.
   - JSON con nlohmann/json.
   - System prompt por asesor.
   - Prompt caching habilitado.

2. **Tier free:**
   - 5 consultas por partida.
   - Counter visible.

3. **Tier paid:**
   - $4.99/mes via Stripe.
   - Toggle en settings.

4. **BYO API key:**
   - Setting → pegar key personal.
   - Ilimitado, sin costo para dev.

5. **Streaming respuesta:**
   - Caracteres aparecen en vivo.

**Tests de aceptación:**
- 5 advisors responden con Claude.
- Costos bajo control: <$0.05 por partida.
- BYO key funciona.
- Toggle on/off.

**Tiempo estimado:** 40 horas.

---

### Sprint 23 (semana 23) — 3 modos de juego activos

**Entregables:**

1. **Sandbox:**
   - Sin objetivos.
   - Configurable: país preset, año, eventos extremos on/off.

2. **Misiones:**
   - 10 misiones implementadas (subset de las 30 del spec).
   - Progreso persistido.

3. **Histórico:**
   - 8 escenarios cargables desde menu.
   - Objetivos por dificultad.

**Tests de aceptación:**
- 3 modos seleccionables desde menú.
- Cada uno tiene sus reglas y feedback.

**Tiempo estimado:** 30 horas.

---

### Sprint 24 (semana 24) — Balance pass final

**Entregables:**

1. **Análisis de telemetría beta:**
   - Eventos más disparados.
   - Comandos más usados.
   - Game overs más frecuentes.

2. **Ajustes a `balance_constants.yaml`:**
   - Inflation curve.
   - Coup probabilities.
   - Scandal severity.

3. **Patcheo de 10+ issues de balance** identificados en beta.

4. **Curva Laffer aplicada a `tax-`** también (no solo `tax+`).

5. **Documentación de cambios:**
   - Changelog detallado para EA launch.

**Tests de aceptación:**
- Tests siguen pasando con nuevos valores.
- 5 testers beta confirman mejora en balance.

**Tiempo estimado:** 30 horas.

---

### Sprint 25 (semana 25) — Marketing assets finales

**Entregables:**

1. **Trailer profesional:**
   - 90s, montaje profesional.
   - Música licenciada o original.
   - Subtítulos en ES y EN.

2. **Screenshots curadas:**
   - 10 finales en 1920x1080.
   - Cada una muestra un sistema/momento distinto.

3. **GIFs animados:**
   - 5 para X/Twitter/Reddit.
   - Cada uno <2MB, <8s.

4. **Press kit:**
   - Logo en SVG + PNG (varios tamaños).
   - Bio del autor.
   - Screenshots.
   - Fact sheet.
   - Quote tester anónima.

**Tests de aceptación:**
- Press kit hospedado en página web propia.
- Assets aprobados por 2 ojos externos.

**Tiempo estimado:** 35 horas (incluye revisiones).

---

### Sprint 26 (semana 26) — Página Steam pulida

**Entregables:**

1. **Descripción larga (500 palabras) ES y EN.**
2. **Tags optimizados:**
   - 20 tags relevantes priorizados.
3. **Capsule images:**
   - Header capsule 460x215.
   - Small capsule 231x87.
   - Library hero 3840x1240.
   - Library icon 256x256.
4. **Video YouTube linkeado.**
5. **Reviews tester pre-publicadas como quotes.**
6. **Precio configurado:** $14.99.
7. **Lanzamiento configurado:** fecha exacta + zona horaria.

**Tests de aceptación:**
- Página revisada por 5 personas externas.
- Visualmente competitiva con páginas tier-2 de Steam.

**Tiempo estimado:** 25 horas.

---

### Sprint 27 (semana 27) — Press outreach

**Entregables:**

1. **Lista de 30 outlets:**
   - 10 tier-1: RPS, PCGamer, IGN, Polygon.
   - 10 tier-2: Indie game sponsorship, niche strategy.
   - 10 hispanos: 3DJuegos, Vandal, Hobby Consolas.

2. **Email personalizado por cada uno:**
   - Asunto: "[Coming Soon Steam] Homo Politicus — Simulador político AAA-indie".
   - Body: pitch en 100 palabras + link a press kit + 1 GIF.

3. **5 streamers contactados:**
   - Quill18 (strategy YouTube).
   - Many a True Nerd.
   - WolfHeavyIndustries.
   - Hispanos: hispanos strategy YouTubers.

4. **Followup en 1 semana.**

**Tests de aceptación:**
- 30 emails enviados.
- Tracking de aperturas y respuestas.
- Mínimo 5 respuestas de interés.

**Tiempo estimado:** 30 horas (mucho de outreach manual).

---

### Sprint 28 (semana 28) — Early Access LAUNCH

**Entregables:**

1. **Day -7:**
   - Email a wishlists ("estamos a 7 días").
   - Post en redes con countdown.

2. **Day -1:**
   - Final smoke test.
   - Verificar Steam config.
   - Backup de saves de testers.

3. **Day 0 — Launch:**
   - 00:00 PST: botón Steam ON.
   - 06:00: AMA en r/IndieDev.
   - 12:00: post en r/strategy.
   - 18:00: stream en vivo del autor.

4. **Day +1 a +7:**
   - Monitoreo intenso de bugs.
   - Patches diarios si hay críticos.
   - Respuesta a 100% de reviews iniciales.

5. **Retrospectiva Hito 3:**
   - `RETRO_HITO_3.md`.

**Métrica de éxito Hito 3:**
- 5.000 copias vendidas primer mes.
- ≥75% reviews positivas.
- Mention en al menos 3 outlets de prensa.

**Tiempo estimado:** 60 horas (sprint más largo, intenso).

---

## 7. HITO 4 — v1.0 Release (semanas 29-52)

**Objetivo:** lanzamiento completo con features pulidas y contenido extendido.
**Versión target:** 1.0
**Precio:** $19.99 USD (+33%).

### Bloque A: Patches semanales (semanas 29-36)

Cada semana:
- **Lunes:** triage de feedback de la semana anterior.
- **Mar-Jue:** desarrollo del patch.
- **Viernes:** release.
- **Sábado-Domingo:** monitoreo.

Patches típicos:
- Sprint 29: bug bash crítico.
- Sprint 30: 5 escenarios extras (USA 1929, Alemania 1933, Sudáfrica 1994 ya están —
  agregar Chile 2019, Brasil 1985).
- Sprint 31: balance pass basado en métricas.
- Sprint 32: 5 líderes extras.
- Sprint 33: 10 eventos extras.
- Sprint 34: Modo Sucesor.
- Sprint 35: Modo Iron Man.
- Sprint 36: Quality of life: hotkeys, autoselect, UI improvements.

### Bloque B: Steam Workshop + Mods (semanas 37-40)

- Sprint 37: Workshop integration técnica.
- Sprint 38: Loader de mods desde Workshop.
- Sprint 39: 3 mods ejemplo del autor.
- Sprint 40: Documentación developer-facing para modders.

### Bloque C: Localización extra (semanas 41-44)

- Sprint 41: Portugués brasileño (contratado o comunidad).
- Sprint 42: Francés.
- Sprint 43: Alemán.
- Sprint 44: Italiano + testing.

### Bloque D: Polish final (semanas 45-48)

- Sprint 45: Performance pass (60fps en 95% hardware).
- Sprint 46: Accessibility (colores, fuentes, narrador para personas con low vision).
- Sprint 47: Telemetría opt-in implementada.
- Sprint 48: Bug bash final.

### Bloque E: v1.0 launch + post-launch (semanas 49-52)

- Sprint 49: Trailer v1.0 (60s nuevo).
- Sprint 50: Marketing pre-launch.
- Sprint 51: **v1.0 LAUNCH** + sale 20% off semana 1.
- Sprint 52: Retrospectiva final + plan año 2.

**Métrica de éxito v1.0:**
- 30-50.000 copias totales acumuladas.
- ≥80% reviews positivas.
- ≥100 mods en Workshop.
- Discord ≥1.000 miembros activos.

---

## 8. Plan de testing exhaustivo

### 8.1 Unit tests (engine)

Por módulo, target de coverage:

| Módulo | Tests target | Actual |
|---|---|---|
| Persistence | 15 | 4 |
| GameOverChecker | 12 | 8 |
| DecisionSystem | 10 | 5 |
| FeedbackBuilder | 8 | 5 |
| ScenarioLoader | 8 | 5 |
| LeaderLoader | 8 | 4 |
| AchievementTracker | 30 | 8 |
| ExpressionEvaluator | 15 | 6 |
| EventLoader | 15 | 4 |
| Advisor | 10 | 6 |
| Localization | 8 | 0 |
| Tutorial | 8 | 0 |
| UIBridge | 12 | 0 |

**Total target:** 160 tests. Actual: 62.
**Trabajo:** 100 tests más, distribuidos en sprints 1-20.

### 8.2 Integration tests

Tests que arrancan el binario y simulan partidas:

- `test_full_game_argentina_1976.cpp`: carga escenario, juega 12 turnos, verifica estado.
- `test_full_game_with_decisions.cpp`: fuerza coup_threat, lo resuelve, verifica.
- `test_save_load_full_roundtrip.cpp`: estado complejo round-trip.
- `test_localization_es_en_parity.cpp`: todas las keys en ambos idiomas.

**Trabajo:** 10 tests de integración, distribuidos en sprints 4-20.

### 8.3 Smoke tests manuales

Cada sprint, checklist:
- [ ] Arranca el binario.
- [ ] Pasa 10 turnos sin crash.
- [ ] Carga escenario.
- [ ] Carga líder.
- [ ] Save → load.
- [ ] Decisión pendiente se resuelve.
- [ ] Game over funciona.

### 8.4 Beta testing (humano)

- Hito 1: 10-20 testers cercanos.
- Hito 2: 100 testers semi-públicos.
- Hito 3: público abierto (Steam EA).
- Hito 4: comunidad activa.

### 8.5 Stress tests

- 100 turnos seguidos sin crash.
- 1000 cargas/guardas seguidas.
- Cargar 10 escenarios en secuencia.
- 5 horas de sesión continua.

---

## 9. Plan de QA y bug bash

### 9.1 Clasificación de bugs

| Severidad | Definición | SLA fix |
|---|---|---|
| **P0 — Critical** | Crash al arrancar, perdida de save, exploit que rompe juego | 24h |
| **P1 — Major** | Funcionalidad clave rota, perdida de progreso parcial | 48h |
| **P2 — Minor** | Funcionalidad secundaria, visual glitch | 1 semana |
| **P3 — Cosmetic** | Typo, alignment | Next minor release |

### 9.2 Workflow de bugs

1. Reporte via GitHub Issues o Discord `#bug-reports`.
2. Triage en 24h (clasificación + reproducción).
3. Issue creado con template:
   - Versión del juego.
   - Sistema operativo.
   - Pasos para reproducir.
   - Logs (auto-attach).
   - Save file relevante.
4. Fix con branch `fix/<issue-number>`.
5. Test que cubre la regresión.
6. Merge + release note.

### 9.3 Templates GitHub Issues

```markdown
**Versión:** 0.5-alpha
**OS:** macOS 14.2
**Pasos:**
1. Cargar escenario chile_1973.
2. Pasar 3 turnos.
3. Aplicar `apologize`.
4. Esperado: severidades bajan.
5. Real: nada cambia.

**Logs:**
[adjunto]

**Save:**
[adjunto]
```

### 9.4 Bug bash days

- Día completo por mes en Hito 4.
- Equipo: autor + 5 testers Discord.
- Foco: 1 área del juego.
- Reportes consolidados en hoja de cálculo.

---

## 10. Plan de marketing semanal

### 10.1 Cadencia de contenido

| Día | Acción |
|---|---|
| Lunes | Devlog en YouTube/blog (15-20 min) |
| Miércoles | Tweet/X con clip de gameplay |
| Viernes | Stream Discord/Twitch (2h) |
| Domingo | Post Reddit (r/IndieDev rotating con r/strategy) |

### 10.2 Eventos especiales

- **Steamfest:** participar si calza con timing.
- **PAX East/West:** virtual presence si presupuesto lo permite.
- **Devlog milestone:** cada cierre de Hito.
- **Indie bundles:** Humble Bundle, Bundle of Holding (post-launch).

### 10.3 Métricas a trackear

- Wishlist count por semana.
- Twitter followers + engagement rate.
- Discord miembros activos.
- YouTube subs + views por video.
- Reddit upvotes por post.
- Visitas página Steam (conversion %).

### 10.4 Press kit hospedado en

`presskit.homopoliticus.com` o equivalente. Estructura:
- Logo (SVG, PNG transparente).
- Header image.
- 10 screenshots HD.
- 3 GIFs.
- Trailer.
- Bio autor.
- Fact sheet (1 página).
- Quotes de testers.
- Contacto prensa.

---

## 11. Plan de contenido

### 11.1 Escenarios target post-launch

**v1.0 ya tiene 8.** Agregar:

1. Chile 2019 — estallido social.
2. Brasil 1985 — fin de la dictadura.
3. España 1981 — 23-F.
4. México 1968 — Tlatelolco.
5. Argentina 2001 — corralito.
6. Bolivia 2003 — guerra del gas.
7. Perú 1992 — autogolpe Fujimori.
8. Colombia 2002 — Uribe inicio.
9. Nicaragua 1979 — triunfo sandinista.
10. Ecuador 2007 — Correa asume.

**Total target v1.0:** 18 escenarios.

### 11.2 Líderes target post-launch

**v1.0 ya tiene 15.** Agregar:

1. Eva Duarte (Argentina).
2. Salvador Allende → ya está.
3. Jair Bolsonaro (Brasil).
4. Cristina Fernández (Argentina).
5. Rafael Correa (Ecuador).
6. Pepe Mujica (Uruguay).
7. Juan Manuel Santos (Colombia).
8. Sebastián Piñera (Chile).
9. AMLO (México).
10. Daniel Ortega (Nicaragua).
11. Evo Morales (Bolivia).

**Total target v1.0:** 26 líderes.

### 11.3 Eventos target

**v1.0 ya tiene 30.** Agregar 20 más:
- 5 eventos de crisis ambiental.
- 5 eventos de tecnología/AI.
- 5 eventos de scandal específicos por país.
- 5 eventos meta (referendum, elecciones, censuras).

**Total target v1.0:** 50 eventos.

### 11.4 DLC plan

**DLC 1: "Latinoamérica" ($4.99) — T+6 meses post v1.0:**
- 5 escenarios LATAM.
- 5 líderes LATAM.
- 10 eventos LATAM-específicos.
- Música regional.

**DLC 2: "Guerra Fría" ($4.99) — T+12 meses post v1.0:**
- 5 escenarios Guerra Fría.
- 5 líderes (Kennedy, Brezhnev, Mao, Tito, Nasser).
- Sistema de bloques geopolíticos extendido.
- Cuba, Vietnam, Afganistán como teatros.

**DLC 3: "Modder's Edition" ($9.99) — T+18 meses:**
- Workshop premium.
- Editor visual de escenarios.
- Tutoriales de modding.
- 5 mods curados.

---

## 12. Plan de comunidad

### 12.1 Discord oficial

**Estructura de canales:**

```
#bienvenida
#reglas
#anuncios
---
#general-es
#general-en
#gameplay-tips
#bug-reports
#feedback
#balance-discussion
---
#mods
#modding-help
#mod-showcase
---
#stream-de-autor
#screenshots
#fan-art
---
#offtopic
```

**Roles:**
- `@founder` (autor).
- `@moderador` (3-5 voluntarios).
- `@beta-tester` (closed alpha + open beta).
- `@early-access` (compraron EA).
- `@modder` (mod publicado).
- `@verified-streamer` (1k+ subs).

### 12.2 Newsletter

Mensual. Contenido:
- Devlog highlights del mes.
- Próximos features.
- Mod del mes.
- Tester destacado.

Hospedado en Buttondown o Substack.

### 12.3 Reddit r/HomoPoliticus

Subreddit propio:
- Reglas claras.
- Pinned: FAQ, roadmap, bug reporting.
- Flair de versiones.
- Bot que sincroniza patches.

### 12.4 Streams del autor

- Frecuencia: 1x/semana, 2h.
- Plataforma: Twitch + YouTube simultáneo.
- Formato: jugando 1 escenario + Q&A + showing dev work.

---

## 13. Plan post-launch

### 13.1 Año 1 post-v1.0

- 4 patches mayores (cada 3 meses).
- DLC 1 lanzado.
- 50% off summer sale.
- Halloween mini-evento (escenarios miedo).
- Black Friday sale.

### 13.2 Año 2

- DLC 2 lanzado.
- Steam Awards push.
- Mobile port investigación.

### 13.3 Año 3+

- DLC 3 lanzado.
- "Homo Politicus 2: World Stage" en planeación.
- Educational edition para escuelas/universidades.

### 13.4 Lifetime targets

- 100.000 copias vendidas en 3 años.
- 500.000 partidas jugadas.
- 1.000 mods publicados.
- 50 streamers regulares.
- Educational adoption en 20 universidades.

---

## 14. Salud del proyecto

### 14.1 Anti-burnout

**Reglas no negociables:**

1. **2 sábados libres por mes obligatorios.** Sin código, sin commits, sin Discord.
2. **Vacaciones de 1 semana cada 3 meses.**
3. **Si una tarea consume más del 200% del tiempo estimado**, parar y reflexionar.
4. **No commits entre 23:00 y 07:00.** Si querés escribir, anotalo y dormí.
5. **Una pasión paralela activa** (deporte, música, libros).

### 14.2 Indicadores de burnout

- Más de 3 días sin querer abrir el editor.
- Frustración irracional con bugs simples.
- Aislamiento social.
- Pérdida de placer al ver el juego corriendo.

**Si aparecen:** pausa de 1 semana. No es debilidad, es prevención.

### 14.3 Plan B

Si en cualquier hito las métricas fallan:

**Falla Hito 1:**
- Opción 1: continuar como freeware/open source.
- Opción 2: pivotar a herramienta educativa.
- Opción 3: archivar con dignidad y portfolio aprendido.

**Falla Hito 2:**
- Revisar feedback de cerca.
- Posiblemente posponer UI launch 2 meses adicionales.

**Falla Hito 3 (EA):**
- Refund period sin penalidad.
- Análisis profundo.
- Versión 1.0 retrasada hasta resolver issues estructurales.

**Falla v1.0:**
- Plan B serio: monetización via DLC modesta.
- O: open source y mantener como hobby.
- No hay vergüenza en cualquiera.

### 14.4 Apoyo

- Discord propio con mentores devs.
- Therapista licenciada (al menos 1x/mes durante el proyecto).
- Grupo cerrado de solo-devs indie.
- Comunidad de Anthropic Builder Program o similar.

---

## 15. Apéndices

### A. Template de retrospectiva por sprint

```markdown
# Retrospectiva Sprint N

**Fecha:** YYYY-MM-DD
**Sprint objetivo:** [objetivo del sprint]

## Entregables completados
- [ ] Item 1 (commit hash)
- [ ] Item 2 (commit hash)

## Entregables NO completados
- [ ] Item 3 — razón:

## Bugs encontrados
- Bug A (P0/P1/P2/P3) — estado:

## Lecciones aprendidas
- Lección 1.
- Lección 2.

## Ajustes al siguiente sprint
- Ajuste 1.

## Mood self-report (1-10)
- Energía: X/10.
- Motivación: X/10.
- Confianza en el proyecto: X/10.
```

### B. Template de issue de bug

```markdown
**Versión:** [ej. 0.5-alpha]
**OS:** [Windows 11 / macOS 14.5 / Ubuntu 22.04]
**Hardware:** [CPU, RAM, GPU si relevante]

## Pasos para reproducir
1.
2.
3.

## Esperado
[Qué esperabas que pasara]

## Real
[Qué pasó realmente]

## Logs
[Adjuntar `~/HomoPoliticus/logs/last.log`]

## Save file
[Adjuntar si relevante]

## Screenshots
[Si aplica]
```

### C. Template de release notes

```markdown
# Homo Politicus v0.X.Y

## Highlights
- 🎉 Feature mayor 1
- 🎉 Feature mayor 2

## Nuevas características
- Feature menor 1
- Feature menor 2

## Mejoras
- Mejora 1
- Mejora 2

## Bugs corregidos
- Fix 1
- Fix 2

## Balance
- Cambio 1 (justificación)

## Conocidos
- Issue 1 (workaround: ...)

## Agradecimientos
Tester A, Tester B...

## Próximo release
ETA: YYYY-MM-DD.
```

### D. Checklist pre-release

```
[ ] Tests 100% pasando
[ ] 0 warnings de compilación
[ ] Binarios para 3 OS
[ ] Save migration testeada con saves de versión previa
[ ] Tutorial completable por nuevo jugador en <15 min
[ ] Trailer actualizado si hay features grandes
[ ] Press kit actualizado
[ ] Devlog publicado anunciando release
[ ] Discord anuncio publicado
[ ] Steam patch notes publicadas
[ ] Soporte 100% para 48h post-release (responder reviews/issues)
```

### E. Glosario

- **EA:** Early Access (Steam).
- **NPS:** Net Promoter Score.
- **Hito:** Milestone, etapa mayor del roadmap.
- **Sprint:** Semana de trabajo dedicada.
- **P0/P1/P2/P3:** Niveles de severidad de bugs.
- **Save scumming:** abusar save/load para evitar consecuencias.
- **TFP:** Total Factor Productivity (economía).
- **YAML:** lenguaje de configuración usado para contenido.
- **LLM:** Large Language Model.
- **Workshop:** Steam Workshop, sistema de mods.

### F. Recursos externos

#### Compras necesarias
- Steamworks: $100 USD (one-time).
- Música licenciada: $300-500.
- SFX bibliotecas: $50 o gratis Creative Commons.
- Server hosting (web + Discord bot): $20/mes.
- Anthropic API costos: $50-100/mes estimado.
- Trademark "Homo Politicus": $250-1500 según país.
- LLC formation: $200-1000 según jurisdicción.

#### Recursos gratuitos a aprovechar
- GitHub Free para repos.
- GitHub Actions CI gratis para repos públicos.
- Discord free.
- Twitter/X free.
- Reddit free.
- itch.io gratis.
- Steam Workshop gratis.
- ImGui open source.
- SFML open source.
- Anthropic Builder Program (aplicar).

### G. Referencias técnicas

- **C++17:** https://en.cppreference.com/w/cpp/17
- **SFML 2.6:** https://www.sfml-dev.org/documentation/2.6.0/
- **Dear ImGui:** https://github.com/ocornut/imgui
- **Catch2 / doctest:** opciones futuras si crecen tests.
- **nlohmann/json:** para LLM advisor.
- **libcurl:** HTTP client.
- **Steamworks SDK:** integración Steam.
- **Anthropic API:** https://docs.anthropic.com/

### H. Referencias de game design

- **Democracy 4 design docs:** https://positech.co.uk/
- **Suzerain postmortem:** ver YouTube GDC.
- **Crusader Kings 3 dev diaries.**
- **Tropico 6 design.**
- **Frostpunk choices system.**

### I. Referencias históricas para escenarios

- Argentina 1976: CONADEP report, Novaro & Palermo.
- Cuba 1959: Hugh Thomas, Richard Gott.
- Chile 1973: Peter Kornbluh, Moulian.
- Venezuela 2013: Carlos Pagni, Moisés Naím.
- Singapur 1965: Lee Kuan Yew memoirs.

### J. Sitios web a comprar/configurar

- `homopoliticus.com` (principal).
- `presskit.homopoliticus.com` (press).
- `mods.homopoliticus.com` (Workshop alternative).
- `docs.homopoliticus.com` (modding docs).

### K. Comunidades a sumarse

- IndieDev Discord.
- /r/IndieDev, /r/gamedev, /r/strategy.
- IndieDB.
- TIGSource forums.
- 80lv (interviews).
- HispaDev (LATAM dev community).

---

## Fin del documento

Este documento representa **12 meses de plan ejecutable**, con detalles a nivel de
sprint. Es un mapa, no una camisa de fuerza. Ajustarlo cuando la realidad lo pida.

**Última palabra:** este plan es ambicioso. La diferencia entre un dev que envía y uno
que abandona no es talento ni recursos: es persistencia con flexibilidad. Pegate a la
estructura pero negociá con vos mismo cuando la realidad cambie.

Vamos.

— Junio 2026
