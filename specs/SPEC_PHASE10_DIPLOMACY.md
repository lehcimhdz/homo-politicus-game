# SPEC Fase 10 — Diplomacia profunda

## Objetivo
Pasar de 3 vecinos a un mundo con 30+ países, bloques geopolíticos dinámicos, tratados multilaterales, organizaciones internacionales y crisis globales.

---

## 10.1 — Mundo con 30+ países

`std::vector<NeighborCountry>` se expande a `std::vector<ForeignCountry>` con propiedades extendidas:

```cpp
struct ForeignCountry {
    std::string id;
    std::string name;
    std::string continent;
    double gdp;
    int population;
    double military_strength;
    double diplomatic_relations_with_player;
    bool is_neighbor;                      // Geografía
    double trade_volume_with_player;
    std::string current_leader_name;
    std::string ideology;                  // "liberal_democracy", "authoritarian_right", etc.
    std::vector<std::string> alliances;
    std::vector<std::string> sanctions_against;
    bool nuclear_armed;
    double soft_power_index;
};
```

### Países iniciales
- 5 superpotencias: USA, China, Russia, EU (UK + Alemania + Francia agrupados), India.
- 10 potencias regionales: Brasil, México, Sudáfrica, Egipto, Turquía, Israel, Arabia Saudí, Japón, Indonesia, Australia.
- 15 países menores variados por continente.

---

## 10.2 — Bloques geopolíticos

```cpp
struct GeopoliticalBloc {
    std::string id;
    std::string name;                    // "Western", "BRICS", "Non-Aligned"
    std::vector<std::string> members;
    double cohesion;                     // 0-1
    std::string leader_country;
    double military_pact_strength;
    double trade_integration;
};
```

Bloques iniciales:
- **Western** (USA + EU + Japón + Australia + Israel).
- **BRICS** (Brasil + Rusia + India + China + Sudáfrica).
- **Non-Aligned** (resto, dinámico).

Mecánica:
- Países cambian de bloque por eventos.
- Bloque ataca a un país → todos sanctions automáticas.
- Player puede pedir membresía (requiere alignment).

---

## 10.3 — Tratados multilaterales

```cpp
struct Treaty {
    std::string id;
    std::string type;        // "trade", "defense", "climate", "human_rights"
    std::vector<std::string> signatories;
    int start_turn;
    int expiration_turn;     // -1 = perpetuo
    std::map<std::string, double> effects_by_country;
};
```

Tipos:
- **Trade**: bilateral o multilateral, baja tariffs.
- **Defense**: cláusula de mutuo auxilio.
- **Climate**: compromiso de reducción CO2.
- **Human Rights**: vinculante, sanciones si se viola.
- **Nuclear**: no-proliferación.

Comandos:
- `treaty_propose <type> <countries>`: propone, países deciden.
- `treaty_join <id>`: unirse a uno existente.
- `treaty_withdraw <id>`: salirse (penalización diplomática).

---

## 10.4 — Organizaciones internacionales

```cpp
struct InternationalOrg {
    std::string id;
    std::string name;
    std::vector<std::string> members;
    double player_influence;       // 0-1
    std::string current_secretary;
};
```

Orgs:
- ONU (todos).
- OMC (90% de países).
- Banco Mundial.
- FMI (con condicionamientos).
- OEA / Unión Africana / ASEAN (regionales).
- OPEP (energéticos).

Mecánica:
- ONU puede votar sanciones contra player.
- FMI ofrece rescate con condiciones (-popularidad, +austeridad).
- OMC arbitra disputas comerciales.

---

## 10.5 — Crisis globales

Eventos que afectan a todos los países simultáneamente:

1. **Crisis financiera global** — todos los GDP -10%.
2. **Pandemia mundial** — pandemic_active en todos.
3. **Crisis energética** — kwh_price ×2 mundialmente.
4. **Cyber-war global** — cyberattack_prob ×3.
5. **Guerra entre superpotencias** — alineación forzada.
6. **Crisis migratoria global** — refugee_inflow ×5.
7. **Crisis climática extrema** — drought/storm/flood en cascada.

---

## 10.6 — Diplomacia detallada con vecinos directos

Para los 3-5 vecinos directos (los actuales), profundizar:
- Frontera caliente: incidentes mensuales si relations < -0.5.
- Reclamos territoriales específicos con mapa.
- Refugiados cruzando con perfil (económicos vs políticos).
- Cooperación binacional en proyectos (represa, puente).

---

## 10.7 — Validación
- 30+ países en el mundo simulado.
- Al menos 3 bloques funcionando.
- 10+ tratados posibles al inicio.
- 6+ orgs internacionales.
- 7+ crisis globales posibles.
- Performance: actualización de todos los países en <50ms por turno.
