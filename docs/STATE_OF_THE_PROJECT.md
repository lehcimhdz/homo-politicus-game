# Estado del proyecto y plan para llegar a producto

Auditoría honesta del estado actual de Homo Politicus (engine + game) y lo que falta para llegar a un producto vendible.

---

## Inventario actual

### `homo-politicus` (engine)
- **9.886 líneas** distribuidas en 19 archivos (motor + tests).
- **24/24 tests** pasando.
- **178 commits** desde el inicio.
- **0 warnings** de compilación.
- 5 módulos extraídos: Game, DecisionSystem, GameOverChecker, Persistence, EventManager.
- Dispatch map para comandos (10 migrados, ~100 todavía en if/else).
- Save/load funcional con subset crítico (30 keys).
- 9 condiciones de fin de partida.
- 5 decisiones reactivas.
- 110+ comandos de jugador.
- ~200 variables interconectadas.

### `homo-politicus-game` (este repo)
- **39 archivos** + 8 specs detallados.
- **5 escenarios históricos** completos con narrativa, stats iniciales, eventos forzados, objetivos por dificultad.
- **10 líderes históricos** con biografías, modificadores, acciones únicas, nemesis traits.
- **30 eventos** con flavor text en 4 archivos temáticos.
- **ES/EN locales** para UI, comandos, decisiones, end conditions.
- **5 documentos de diseño**: tutorial (5 misiones), modos (6 modos), balance (constantes + exploits), 40 logros, formato de feedback.

---

## Qué está sólido

✅ **Motor de simulación**: profundo, testeable, modular en su núcleo.
✅ **Cobertura conceptual**: prácticamente todos los aspectos del estado moderno modelados.
✅ **Contenido inicial**: 5 escenarios reales con investigación seria, no genéricos.
✅ **Documentación de diseño**: el "qué hacer" está claro y consistente.
✅ **Tests**: hay red de seguridad para refactorizar.

## Qué falta para ser producto vendible

### Críticos (bloqueantes de Steam)

1. **UI gráfica**: no se vende terminal. ~2-3 meses con SFML+ImGui o 3-4 con Godot.
2. **Loader de contenido YAML**: hoy los YAML son texto inerte. Hay que escribir parser que los convierta en `Country` y `Event` C++.
3. **Onboarding implementado**: el tutorial está escrito, no codeado.
4. **Sistema de logros codeado**: 40 logros definidos en YAML, ninguno tracking real.
5. **Save versionado robusto**: el subset crítico actual rompe partidas largas.

### Importantes (pulido producto)

6. **Refactor de `Game.cpp` por sistema**: 7.900 líneas siguen monolíticas. Bloquea velocidad de desarrollo.
7. **Externalizar constantes**: 200+ valores hardcodeados. `balance_constants.yaml` ya existe pero no se consume.
8. **Parchear exploits**: los 5 documentados están sin fix.
9. **Curva Laffer y feedback legible**: documentados, no implementados.
10. **Sound + arte mínimo**: nada todavía.

### Nice-to-have

11. Fases 9-12 enteras (regiones, 30 países, agentes con nombre, modding).
12. Localización a más idiomas (PT, FR, IT).
13. Achievements de Steam.
14. Cloud saves.
15. Trailer, screenshots, página web.

---

## ¿Se puede ampliar specs y seguir ejecutando?

Sí, en estas direcciones (orden de impacto):

### Ampliación A: Implementar loader de contenido (alto impacto)
Hacer que los 28 YAML actuales **se carguen y afecten el juego**. Necesario para que el contenido sirva.

**Trabajo:** 2-3 días.
**Resultado:** poder elegir escenario al inicio del juego desde menú.

### Ampliación B: Más escenarios y líderes (impacto medio)
Pasar de 5 a 15 escenarios, 10 a 25 líderes. Material clave para retención.

**Trabajo:** 1-2 días por escenario (investigación + redacción).
**Resultado:** más rejugabilidad.

### Ampliación C: Implementar 1 de las fases 9-11 (alto impacto profundo)
La que más diferencia daría: **Fase 11 — agentes individuales**. Convertir abstracciones en personas con nombre. Es lo que hace memorable a Crusader Kings.

**Trabajo:** 3-4 semanas para el sistema completo.
**Resultado:** profundidad narrativa real.

### Ampliación D: Pulir balance y feedback (impacto inmediato)
Implementar formato de feedback documentado, parchear los 5 exploits, externalizar constantes.

**Trabajo:** 1 semana.
**Resultado:** sensación de pulido muchísimo mejor.

---

## Ruta crítica realista para "producto listo Steam"

```
Mes 1: Refactor por sistema + loader YAML + curva Laffer + feedback legible
Mes 2: UI gráfica básica (5 paneles, modal, mapa simple)
Mes 3: UI pulida + tutorial implementado + logros + save versionado
Mes 4: Playtesting cerrado (50 testers), balance pass, bug bash
Mes 5: Polish, marketing assets, página Steam, trailer
Mes 6: Early Access launch + parches semanales
```

A medio tiempo con IA como co-piloto: **6-9 meses calendar** realistas.
A tiempo completo solo: **4-5 meses**.

---

## Cuándo "terminar"

Mi recomendación: **NO terminar** en sentido absoluto.

El motor está bien diseñado para crecer iterativamente. Pero hay un **punto de buena llegada de v1.0**:

### Definición de Done para v1.0 Early Access
- UI gráfica funcional (no AAA, sí jugable).
- Tutorial implementado y testeable por usuario nuevo.
- 5 escenarios cargables desde menú.
- 10 logros funcionales (no los 40).
- Save/load robusto.
- 0 crashes conocidos.
- Performance: 60 fps en hardware 2018.
- Página Steam wishlistable.

### Definición de Done para v1.0 lanzamiento
- 30 logros funcionales.
- 10 escenarios.
- Localización ES + EN testeada.
- 50 horas de gameplay sin contenido duplicado.
- Soporte de mods básico (cargar carpeta externa).
- Reviews positivas en Early Access (>75% positivo).

### Después de v1.0
- DLC con escenarios extras ($4.99 cada uno).
- Modo cooperativo si la base lo pide.
- Localización a 5 idiomas más.
- Soporte Workshop de Steam.

---

## Decisión recomendada AHORA

Antes de seguir generando specs, **lo más valioso es ejecutar la Ampliación A** (loader YAML) y la **D** (balance + feedback). Eso te da:
- Un juego que **realmente** usa el contenido escrito.
- Feedback que cualquier tester pueda entender.
- Base sólida para empezar la UI gráfica.

Luego, decidir en frío si querés:
- (i) ir a UI gráfica (Fase 5),
- (ii) profundizar el motor (Fase 9-11),
- (iii) abrir alfa cerrada con la versión terminal a 10-20 amigos para feedback temprano.

La opción (iii) es la que más data te da por menos esfuerzo.

---

## Estado emocional honesto

Llevás ~14 horas de desarrollo asistido. Tenés:
- Un motor más profundo que muchos juegos comerciales del género.
- Contenido inicial de calidad investigación universitaria.
- Documentación que sobrevive a cualquier handover futuro.
- 178 commits que cuentan la historia.

Es un proyecto **serio**. La pregunta no es "¿es bueno?" sino "¿qué tan lejos lo querés llevar?".

Si la respuesta es **Steam**, son 6-9 meses más con prioridades claras.
Si es **portfolio para CS**, ya está listo: muestra arquitectura, tests, modularización, contenido.
Si es **hobby de aprendizaje**, seguilo iterando sin presión.

Las tres son válidas.
