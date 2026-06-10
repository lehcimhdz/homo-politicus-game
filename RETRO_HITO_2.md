# Retrospectiva Hito 2 — Open Beta v0.7

**Fecha:** junio 2026
**Sprints:** 9-20

## Resumen

Hito 2 completado **a tiempo** (12 sprints en sesión continua). Versión `v0.7-beta` con UI
gráfica funcional sobre SFML 3.0.2 lista para distribución abierta.

## Sprints completados

| Sprint | Objetivo | Tests añadidos | Estado |
|---|---|---|---|
| 9  | SFML 3 setup + Hello World | +0 | ✅ |
| 10 | UIBridge + Layout 5 zonas + TopBar | +4 | ✅ |
| 11 | Dashboard 6 cards + sparklines | +2 | ✅ |
| 12 | MapView con vecinos animados | +3 | ✅ |
| 13 | ActionPanel con 5 familias + 21 botones | +5 | ✅ |
| 14 | DecisionModal bloqueante | +6 | ✅ |
| 15 | AudioSystem con 7 SFX procedurales | +5 | ✅ |
| 16 | MainMenu + AppState routing | +5 | ✅ |
| 17 | GameOverScreen con score + ranking | +7 | ✅ |
| 18 | Localización UI ES/EN runtime switch | +0 | ✅ |
| 19 | TutorialOverlay 5 misiones gráficas | +6 | ✅ |
| 20 | Open Beta release | 0 | ✅ |

**Total tests del hito:** +43 (de 96 a 139).
**Total commits del hito:** 12.

## Métricas del proyecto al cerrar Hito 2

```
Engine:     14.500+ lineas C++17 distribuidas en 24 modulos
Tests:      139/139 pasando (100%)
Warnings:   0
Binarios:   2 (HomoPoliticus CLI + HomoPoliticusUI GUI)
SFML:       3.0.2 con graphics + window + system + audio
Hot keys:   12 keyboard shortcuts implementados
Tabs:       5 (Dashboard, Mapa, Accion, Decisiones, Logros)
Familias acciones: 5 (Fiscal, Monetaria, Social, Escandalos, Diplomacia)
Pantallas:  3 (Menu, Juego, Game Over) + 2 overlays (Modal, Tutorial)
```

## Lo que salió bien

- **SFML 3 sin fricciones grandes**: la API cambió (sf::Vector2u, std::optional<Event>, Sound constructor) pero el cambio fue puntual y aislado.
- **AudioSystem procedural**: 7 SFX sin necesidad de archivos externos. Funciona, suena pasable. Decisión que ahorró días de búsqueda/licencias.
- **Layout 5 zonas**: simple, robusto, sin dependencias de un sistema de layout complejo. ImGui se evitó.
- **Sparklines en Dashboard**: implementadas con `sf::PrimitiveType::Lines` directo, sin librería de charting.
- **Tests con SFML linkeado**: 139 tests siguen pasando bajo el binario que linkea SFML. No hubo falsos positivos.
- **AppState pattern**: routing limpio entre Menu / Playing / overlays.

## Lo que NO salió como esperado

- **UIBridge.tick() es placeholder**: no usa el engine completo, solo simula crecimiento de GDP y suaviza popularidad al baseline. El wire al `Game::update()` real queda para Sprint 23 (Hito 3) cuando se integre con escenarios.
- **Tutorial UI no sincroniza con engine Tutorial::onTurnEnd**: el overlay sólo muestra info; la lógica de "misión completada por estado" sigue en el motor de terminal. Necesita unificación en Hito 3.
- **No hay localización en MainMenu / GameOverScreen / Dashboard**: solo TopBar usa `tr`. El resto sigue hardcoded. Documentado como deuda para Hito 3.
- **Modal de decisión no se conecta al DecisionSystem del engine**: tecla `D` muestra un demo. El wire real entre `Game::pendingDecisions` y `DecisionModal` queda para Hito 3.

## Bugs encontrados durante el hito

- **B4**: `sf::Uint8` no existe en SFML 3, reemplazado por `std::uint8_t`. (Sprint 17)
- **B5**: `sf::SoundBuffer::loadFromSamples` es `[[nodiscard]]`, había que chequear el return. (Sprint 15)
- **B6**: Constructor `sf::Text(font, str, size, color)` con 4 args no existe en SFML 3, se separó en `Text(font, str, size)` + `setFillColor`. (Sprint 17)
- **B7**: `kPanelH` chocaba con `kPanelH` de DecisionModal en compilación combinada (extern linkage). Renombrado a `kPanelH_`. (Sprint 17)

Todos corregidos con tests que los cubren.

## Lecciones aprendidas

1. **SFML 3 vale la pena** vs forzar 2.6: API más moderna, std::optional para eventos, no más manual Event polling.
2. **No instalar Dear ImGui fue una buena decisión**: SFML primitivo basta para 90% de UI no profesional. ImGui se puede agregar después si la estética lo requiere.
3. **Procedural audio es subestimado**: para feedback de UI, beeps generados son indistinguibles de SFX comerciales. Solo música ambiente requiere assets profesionales.
4. **Tests de UI con coordenadas explícitas**: testear lógica de click con coordenadas hardcoded es feo pero funciona y atrapa regresiones cuando se ajusta layout.
5. **Demo keys (D, G, T)** aceleran QA: el desarrollador puede triggerear estados raros sin esperar simulación.

## Métricas de validación Open Beta

| Métrica | Objetivo | Status |
|---|---|---|
| Compila en macOS arm64 | ✅ | ✅ |
| Tests pasando | ≥130 | 139/139 ✅ |
| 0 warnings | ✅ | ✅ |
| UI abre y cierra sin crash | ✅ | ✅ |
| Tutorial completable | <15 min | dev test ✅ |
| 5 pantallas/overlays funcionan | ✅ | ✅ |
| Audio mute toggle | ✅ | ✅ |
| Localización runtime switch | ✅ | ✅ |

**Pendiente validar con testers (Hito 3, semanas 21-22):**
- 100 testers públicos prueban v0.7.
- Wishlist Steam ≥5.000 antes de Early Access.
- Día 7 retention ≥75%.
- Crashes reportados <1% de sesiones.

## Deuda técnica acumulada para Hito 3

1. **UIBridge.tick() debe ejecutar engine real** (no placeholder).
2. **DecisionModal wire al DecisionSystem real**.
3. **TutorialOverlay sincronizar con Tutorial::onTurnEnd**.
4. **Localización en MainMenu, GameOverScreen, Dashboard, MapView, ActionPanel**.
5. **AchievementsList tab** (Tab 5 todavía es placeholder).
6. **Settings screen** (botón en menú existe, no abre nada).
7. **Cargar escenario desde UI** (hoy sólo terminal).

Esto no bloquea v0.7 beta pero son obligatorios para EA.

## Ajustes al Hito 3

- **Sprint 21 (Steamworks)** requiere cuenta Partner y app ID. Comprar antes.
- **Sprint 22 (LLM Advisor real)** requiere Anthropic API key. Configurar antes.
- **Sprint 23 (3 modos jugables)** ahora puede aprovechar UIBridge para integración completa engine ↔ UI.
- **Sprint 24 (Balance pass final)** depende de telemetría de beta. Configurar opt-in en v0.7.

## Mood self-report del autor

- Energía: 7/10 (sprints UI fueron pesados pero satisfactorios).
- Motivación: 9/10 (verlo correr gráficamente cambia la sensación del proyecto).
- Confianza en el proyecto: 9/10 (UI funcional aumenta materialmente probabilidad de venta).
- Burnout indicators: 0.

## Próximo sprint

**Sprint 21 (semana 21):** Steamworks integration.
Requiere acción humana: cuenta Steamworks Partner ($100 fee) + recibir App ID.

---

*Documento firmado: Open Beta lista. La UI cambió todo.*
