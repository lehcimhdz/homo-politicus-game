# EXEC 5 — AI Advisor scaffold

## Objetivo
Estructura interna para asesores LLM. **No** llamada real a Claude/OpenAI en este pase (requiere libcurl + API key + dinero). Mock que devuelve respuestas canned, listo para wiring posterior.

## Alcance
- 5 advisor personalities como datos (no LLM real):
  - `hacienda_minister` — análisis fiscal canned.
  - `intel_chief` — analiza presiones, devuelve narrativa.
  - `spokesperson` — redacta comunicado plantilla.
  - `political_advisor` — sugiere movida basada en estado.
  - `opposition_critic` — devuelve crítica plantilla.
- Comando `ask <advisor_id> <pregunta>` (pregunta opcional, ignorada en mock).
- Cada advisor genera respuesta inspeccionando el Country (no LLM, lógica heurística).
- Listo para inyectar `LLMProvider` interface en futuro.

## Lo que queda fuera (TODO)
- Llamada real a API (Anthropic/OpenAI).
- Streaming.
- Prompt caching.
- Pago/tiers.
- Conversación multiturno.

## Archivos a crear
- `include/Advisor.hpp` (interfaz + 5 implementaciones canned)
- `src/Advisor.cpp`
- `tests/test_advisor.cpp`

## API
```cpp
class Advisor {
public:
    virtual ~Advisor() = default;
    virtual std::string id() const = 0;
    virtual std::string name_es() const = 0;
    virtual std::string respond(const Country& c, const std::string& question) const = 0;
};

namespace Advisors {
    std::vector<std::unique_ptr<Advisor>> all();
    Advisor* findById(const std::string& id);
}
```

## Implementaciones canned (heurísticas)

### HaciendaMinister
```cpp
if (inflation > 0.5) return "Ministra: inflación crítica. Recomiendo congelar emisión...";
if (debt_to_gdp > 0.8) return "Ministra: deuda peligrosa. Considera reestructurar...";
if (in_recession) return "Ministra: recesión activa. Estímulo o paciencia, decidí.";
return "Ministra: situación fiscal estable. Aprovechá para ahorrar al SWF.";
```

Misma estructura para los otros 4.

## Validación
- 5 advisors instanciables.
- Cada uno devuelve respuesta no vacía con cualquier Country.
- Comando `ask hacienda_minister` funciona.
- 5 tests unitarios (uno por advisor).
- TODO comment claro indicando dónde inyectar `LLMProvider` real.
