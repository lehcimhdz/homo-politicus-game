# SPEC Fase 5 — UI gráfica (depth: SimCity-class)

## Objetivo
Reemplazar la salida terminal por una UI 2D rica, con paneles persistentes, gráficos temporales, mapa nacional + vecinos, modales de decisión y feedback visual de eventos críticos.

## Engine elegido (decisión de arquitectura)

| Opción | Pros | Contras | Recomendación |
|---|---|---|---|
| SFML 2.6 + Dear ImGui | C++ puro, widgets ricos vía ImGui, ligero | Estética "tool" salvo trabajo extra | **Recomendado** para v1 |
| Godot 4 + GDExtension C++ | Editor visual, multiplataforma, viable Steam, animaciones nativas | Reaprender Godot, integración C++ requiere boilerplate | Para v2 / refactor mayor |
| Qt 6 | Widgets profesionales, signals/slots | Licencia comercial requiere atención, peso | Solo si se distribuye dentro de empresa |

**Decisión:** SFML + ImGui. `brew install sfml` y descargar `imgui.h` + `imgui-SFML.h`.

---

## Layout principal (1280×800 mínimo)

```
+-----------------------------------------------------------+
| TopBar  Turno 12  | Pop 64% | GDP $620M | Inflación 4%   |
+--------------+--------------------------------+-----------+
|              |                                |           |
| SidebarLeft  |    MainPanel (cambia por tab)  | SidebarRt |
|              |                                |           |
| - Welfare    |    [Tab: Dashboard]            | Eventos   |
| - Economy    |    Gráficos temporales         | activos   |
| - Politics   |    Indicadores clave           |           |
| - Security   |                                | Decisiones|
| - Infra      |    [Tab: Mapa]                 | pendientes|
|              |    Vecinos visuales            |           |
| - Acciones   |                                | Próximas  |
| - Modos      |    [Tab: Acción]               | elecciones|
|              |    Comandos contextuales       |           |
+--------------+--------------------------------+-----------+
| BottomBar  Mensajes recientes / log de turno              |
+-----------------------------------------------------------+
```

### Componentes
1. **TopBar**: turno, popularidad (color rojo si <30%), GDP, inflación, alerta intermitente si hay decisión crítica.
2. **SidebarLeft**: 5 botones por sistema + acciones rápidas.
3. **MainPanel**: tabs Dashboard / Mapa / Acción / Logs.
4. **SidebarRight**: eventos activos (chips), decisiones pendientes, contador elecciones.
5. **BottomBar**: scroll de los últimos 6 mensajes del log.

---

## Pantalla 1 — Dashboard (tab principal)

Grid 2×3 con cards:

- **Card 1: Popularidad**: gauge circular + sparkline últimos 20 turnos. Tooltip muestra trend + factores.
- **Card 2: Economía**: GDP (línea), inflación (línea), desempleo (línea) últimos 40 turnos.
- **Card 3: Presiones**: 5 barras horizontales (cong/jud/mil/pop/intl) con umbrales marcados (0.7 warning, 0.85 critical).
- **Card 4: Escándalos**: lista de severidades > 0 con barra de cobertura mediática.
- **Card 5: Sistemas de país**: 5 mini-gauges (welfare/economy/politics/security/infra agregados 0-1).
- **Card 6: Vecinos**: 3 chips con bandera + relación diplomática + estado (paz/tensión/guerra).

Cada card es click-through al panel detallado del sistema.

---

## Pantalla 2 — Mapa

- País central como polígono coloreado por estabilidad (verde→rojo).
- 3 vecinos como polígonos adyacentes, con línea de relación coloreada.
- Líneas comerciales animadas (puntos viajando) si trade_volume > umbral.
- Iconos de eventos sobre el país (☠ pandemia, ⚔ guerra, 🌪 tormenta).
- Click en vecino → modal con stats bilaterales y comandos `improve_relations/threaten/trade_deal`.

---

## Pantalla 3 — Acción

Tabs por familia de comandos (las 10 familias del `help`):
- Fiscal, Recursos, SWF, Comercio, Ambiental, Monetario, Política, Diplomacia, Vecinos, Escándalos.

Cada comando es un botón con:
- Icono + nombre legible.
- Tooltip con efectos esperados (de la tabla de balance).
- Coste si lo tiene (ej. `cover_up` muestra `lobbying_cost × 10 = $500K`).
- Disabled si no aplica (ej. `cover_up` deshabilitado si no hay escándalos).

---

## Modal de decisión

Cuando aparece `PendingDecision`, overlay modal en el centro:

```
+-------------------------------------------+
|  🚨  DECISIÓN REQUERIDA                   |
|                                           |
|  Texto del prompt en una o dos líneas.   |
|  Contexto extra (qué causó esto, qué     |
|  consecuencias previsibles).             |
|                                           |
|  [ Opción A — descripción ]              |
|  [ Opción B — descripción ]              |
|  [ Opción C — descripción ]              |
|  [ Skip — pospone, -credibilidad ]       |
+-------------------------------------------+
```

Cada botón muestra deltas esperados al hover (-popularidad, +presión militar, etc.).

---

## Animaciones / feedback de eventos

- **Golpe**: pantalla flickea rojo 0.5s, ícono militar gigante.
- **Pandemia**: máscaras flotando sobre el mapa, contador de muertos.
- **Sequía**: textura amarilla cubriendo regiones rurales.
- **Elección ganada**: confeti + jingle.
- **Game Over**: blackout + pantalla final con badge de la condición disparada.

---

## Sonido

- Música ambiente loop neutro (`docs/audio/ambient.ogg`).
- SFX por categoría de evento (alerta crisis, victoria, derrota).
- Toggle mute en TopBar.

---

## Estado del game loop con UI

El loop sigue siendo `run → processEvents → update → render`. Cambia:
- `processEvents()` lee eventos SFML (click, key) y los traduce a comandos string que pasan al dispatch map del engine.
- `update()` no cambia (es el motor).
- `render()` dibuja la escena en lugar de printf.

---

## Validación
1. Arrancar binario abre ventana 1280×800.
2. Pasar turno con botón → dashboard se actualiza.
3. Decisión pendiente → modal aparece, opciones disparan handlers correctos.
4. Click en vecino → modal bilateral funciona.
5. Game over → pantalla final con condición correcta.
6. Resolution scaling: ventana redimensionable mantiene layout.
7. 60 fps sostenidos en hardware promedio.
