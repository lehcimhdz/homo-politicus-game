# EXEC 1 — AchievementTracker

## Objetivo
Implementar tracker C++ que evalúe condiciones de logros por turno y registre los desbloqueados. Subset de los 40 del YAML (las que requieren solo estado actual del Country; las complejas que necesitan history tracking quedan TODO).

## Alcance realista en esta sesión
- 15 logros simples (de los 40), evaluables con solo el estado actual.
- AchievementTracker stateful: lleva `unlocked: set<string>`.
- Llamado al final de update() y al renderEndScreen().
- Persistencia: añadir lista de logros a save/load.
- Comando `achievements` que lista logros desbloqueados.
- 5 tests unitarios.

## Lo que queda fuera (TODO)
- 25 logros con condiciones complejas (cumulativo, "5 consecutivos", history tracking).
- Carga del YAML real — lista hardcoded en este pase; loader YAML después.
- Steam achievements wiring — solo local.

## Archivos a crear
- `include/AchievementTracker.hpp`
- `src/AchievementTracker.cpp`
- `tests/test_achievements.cpp`

## API
```cpp
class AchievementTracker {
public:
    void evaluate(const Country& c, int turn, EndCondition end);
    bool isUnlocked(const std::string& id) const;
    std::vector<std::string> unlockedList() const;
    void serialize(std::ostream&) const;
    void deserialize(std::istream&);
private:
    std::unordered_set<std::string> unlocked;
};
```

## 15 logros a implementar
1. `first_year` — turn >= 12
2. `decade_in_power` — turn >= 40
3. `historic` — turn >= 100
4. `economic_miracle` — gdp now / gdp start >= 1.5
5. `inflation_tamed` — inflation < 0.03 al turno actual (simplificación: spec original era "durante 20 turnos")
6. `default_d` — credit_rating == D
7. `swf_titan` — sovereign_wealth_fund > 100M
8. `caudillo` — authoritarian_actions_count >= 10
9. `constitutionalist` — end_condition != NONE AND authoritarian_actions == 0
10. `soft_power` — diplomatic_prestige > 0.9
11. `regional_allies` — count(neighbors.diplomatic_relations > 0.5) >= 3
12. `non_aligned` — non_aligned_index > 0.8
13. `ended_by_coup` — end_condition == COUP_SUCCESS
14. `ended_by_nuke` — end_condition == NUCLEAR_ANNIHILATION
15. `speedrun_collapse` — end_condition != NONE AND turn < 10

## Validación
- 15 logros detectables por unit test (montar Country con estado que dispara cada uno).
- Comando `achievements` muestra los desbloqueados.
- Persistencia roundtrip incluye logros.
