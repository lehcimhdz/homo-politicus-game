# EXEC 4 — EventLoader con parser de expresiones

## Objetivo
Cargar los 30+ eventos YAML y evaluarlos cada turno. Si el trigger se cumple y `probability_per_turn` rolea, aplicar efectos y posiblemente encolar una decisión.

## Complejidad real
**Alta.** El YAML usa expresiones tipo:
```yaml
trigger:
  expression: "country.economy.inflation > 0.5 AND country.economy.monetary_emission > 0.3"
```

Necesito un mini-parser de expresiones con: paths a structs, comparadores, AND/OR/NOT, paréntesis.

## Alcance realista
- Parser que soporte:
  - Paths: `country.<system>.<field>` (resuelven a double).
  - Operadores: `>`, `<`, `>=`, `<=`, `==`, `!=`.
  - Conectores: `AND`, `OR` (sin paréntesis para v1).
  - Booleanos: `true`, `false`.
- Aplicación de `effects` simples:
  - `gdp: "*0.94"` → multiplicar.
  - `popularity: "-0.10"` → sumar.
  - `pandemic_active: true` → set bool.
- `decision_branches` → encola PendingDecision con esos labels.
- 5 eventos seleccionados para pase inicial (no los 30, validar el motor primero):
  - `commodity_boom`, `global_crash`, `urban_terrorist_attack`, `severe_drought`, `attentate_opposition`.

## Lo que queda fuera (TODO)
- Paréntesis en expresiones (`(A AND B) OR C`).
- Helpers tipo `external_event`, `any_neighbor.X`.
- Cooldowns.
- Forced_events de escenarios.
- Los 25 eventos restantes.

## Archivos a crear
- `include/ExpressionEvaluator.hpp` + `src/ExpressionEvaluator.cpp`
- `include/EventLoader.hpp` + `src/EventLoader.cpp`
- `tests/test_expression.cpp`
- `tests/test_event_loader.cpp`

## API
```cpp
namespace ExpressionEvaluator {
    bool evaluate(const std::string& expr, const Country& c);
}

namespace EventLoader {
    struct Event {
        std::string id;
        std::string name_es;
        std::string trigger_expr;
        double prob_per_turn;
        std::vector<std::pair<std::string, std::string>> effects; // (path, value)
        std::vector<PendingDecision> branches;
    };
    std::vector<Event> loadAll(const std::string& dir);
    void applyEffects(const Event& e, Country& c, std::vector<PendingDecision>& queue, std::mt19937& rng);
}
```

## Parser approach
Tokenizer simple → AST con 2 niveles (cláusulas AND/OR de comparaciones). Sin recursión.

Resolución de paths: tabla `unordered_map<string, function<double(const Country&)>>` precomputada con los 50 campos más usados.

## Validación
- 5 tests del evaluador (cada operador).
- 3 tests del loader (parsea YAML, lee effects, encola branches).
- Smoke test in-game: forzar inflación a 0.6, monetary_emission a 0.4 → evento `hyperinflation` se dispara visible.
