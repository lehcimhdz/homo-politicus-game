# EXEC 3 — Dispatch migration

## Objetivo
Migrar 20 comandos más usados del legacy if/else al dispatch map. Sin cambio de comportamiento, solo movimiento de código. Acelera procesamiento (O(1) lookup vs O(n) cadena de if).

## Estado actual
- Dispatch map: 8 handlers registrados (next, exit, save, load, tax+, tax-, decisions, skip_decision).
- Legacy if/else: ~95 comandos restantes.

## 20 comandos a migrar
Priorizados por frecuencia de uso estimada (los del help más usados):

### Fiscal/Welfare (6)
1. `wage-`
2. `retire+`
3. `retire-`
4. `invest_health`
5. `invest_security`
6. `invest_education`

### Ideológicos/Sociales (4)
7. `worship+`
8. `worship-`
9. `minority+`
10. `minority-`

### Monetarios (4)
11. `autonomy+`
12. `autonomy-`
13. `interest+`
14. `interest-`

### Escándalos (4)
15. `cover_up`
16. `scapegoat`
17. `counter_narrative`
18. `apologize`

### Diplomáticos (2)
19. `diplomacy+`
20. `diplomacy-`

## Estrategia técnica
1. Mover el cuerpo de cada `else if (command == "X") { ... }` a una lambda registrada en `registerCommands()`.
2. Eliminar el bloque legacy correspondiente.
3. Mantener exactamente el mismo comportamiento (incluso los `std::cout` literales).
4. Verificar smoke test después de cada bloque (5 commits, 4 comandos por commit).

## Riesgos
- Algunos comandos capturan `this` y modifican estado complejo. Lambdas deben tomar `this` por referencia.
- Si un comando lee desde `std::cin` (segundo argumento), no se puede migrar al map fácil — quedar fuera. Ej `threaten`, `scenario`, `improve_relations` quedan en legacy.
- Mantener `help`, `status_brief` en su lugar actual (tienen lógica de formato larga).

## Validación
- Tests siguen pasando 34/34.
- Smoke test manual: 5 comandos migrados emiten output idéntico al legacy.
- Tamaño de `processEvents()` reducido en ~600 líneas.
