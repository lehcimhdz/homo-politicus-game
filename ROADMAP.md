# Roadmap — homo-politicus-game

This repo owns Fases 5 a 8 of the master roadmap. The engine (`homo-politicus`) owns Fases 3-4.

## Fase 5 — UI gráfica
Requires `brew install sfml` or choosing Godot. Not started until engine API freezes.
- Pantalla principal con 5 paneles (uno por sistema).
- Gráficos temporales: GDP, popularidad, presiones.
- Mapa de país + vecinos.
- Panel modal para decisiones.

## Fase 6 — Diseño de juego (en ejecución)
- Tutorial interactivo: guion de 5 misiones guiadas.
- Modos de juego: Sandbox, Misiones, Histórico.
- Balance: exploits documentados, tabla de constantes tuneables.
- Logros y scoring.
- Feedback legible: cada acción debe mostrar deltas.

## Fase 7 — Contenido (en ejecución)
- **30+ eventos** con flavor text en YAML.
- **10 líderes históricos** con stats iniciales.
- **5 escenarios prefabricados** (Argentina 1976, Cuba 1959, Venezuela 2013, Chile 1973, Singapur 1965).
- **Localización ES/EN**.

## Fase 8 — Calidad de producto
- Save versionado.
- Auditoría UB con sanitizers.
- Packaging Win/macOS/Linux.
- Marketing: trailer, screenshots, página Steam.
- Early Access.

---

## Order of execution in this session

1. Estructura + specs + licencias.
2. **Fase 7 primero** (contenido es data, no requiere dependencias):
   - 5 escenarios YAML.
   - 10 líderes YAML.
   - 30 eventos YAML.
   - Localización inicial ES/EN.
3. **Fase 6 después** (diseño escrito):
   - Tutorial: 5 misiones en Markdown.
   - Modos de juego documentados.
   - Tabla de balance: exploits + constantes.
   - Sistema de logros propuesto.
4. Fase 5 queda pendiente hasta decisión de engine gráfico.
5. Fase 8 queda fuera de scope inmediato.
