# SPEC Fase 11 — Agentes individuales con nombre

## Objetivo
Convertir abstracciones ("el congreso", "el ejército") en personas concretas con nombre, edad, ambición, lealtad y vínculos. Profundidad estilo Crusader Kings.

---

## 11.1 — Modelo de agente

```cpp
struct PoliticalAgent {
    std::string id;
    std::string name;             // "María Fernanda Bonomi"
    int age;
    std::string role;             // "Minister of Economy", "General", "Senator"
    std::string party_affiliation;
    double loyalty_to_leader;     // -1 a +1
    double competence;            // 0-1
    double corruption_tendency;   // 0-1
    double ambition;              // 0-1 (alto = quiere tu puesto)
    std::vector<std::string> traits; // "charismatic", "veteran", "scholar"
    std::vector<std::string> scandals_personal;
    int years_in_role;
    std::string mentor_id;
    std::vector<std::string> protégés;
};
```

---

## 11.2 — Gabinete del jugador

15-20 ministros con nombres:
- Economía, Finanzas, Defensa, Interior, Salud, Educación, Trabajo, RR.EE., Justicia, Agricultura, Energía, Tecnología, Cultura, Ambiente, Vivienda.

Cada ministro:
- Aporta `competence * 0.05` al rendimiento de su sistema.
- Si `corruption_tendency > 0.6` → desbloquea escándalo de su área.
- Si `loyalty < 0.2` → puede renunciar o filtrar info.
- Cambiar ministro (decreto) cuesta capital político.

### Comandos nuevos
- `cabinet`: lista gabinete.
- `replace_minister <role> <new_agent_id>`.
- `inspect_agent <id>`.

---

## 11.3 — Generales del ejército

5-8 generales con nombres:
- `loyalty_to_leader`, `regional_command` (asigna región), `political_ambition`.
- General ambicioso + bajo `civilian_military_control` → conspiración.
- Promoción y degradación afectan moral de las tropas.

---

## 11.4 — Congresistas (100 nombres)

100 legisladores con:
- Partido, ideología (econ + social), circunscripción regional, ambición.
- Votos legislativos calculados a partir del aggregate de los 100 (no de `congressional_support` arbitrario).
- Algunos son "swing votes" — el jugador puede comprarlos vía lobbying.

### Comandos nuevos
- `bribe_legislator <id>`: aumenta lealtad, costo, riesgo de escándalo.
- `coalition_breakdown`: ver qué partidos apoyan o no.

---

## 11.5 — Jueces de la corte suprema

7-9 jueces nombrados:
- Inclinación política, edad (jubilación próxima abre vacante).
- Composición decide rulings_against_state_prob.
- Nombramiento de juez nuevo es decisión modal con backlash legislativo.

---

## 11.6 — Líderes de oposición

3-5 figuras opositoras con nombre, popularidad propia, partido, escándalos.
- Si su popularidad > la del jugador en elecciones → derrota electoral.
- Pueden hacer alianzas, traicionarse mutuamente.
- Asesinato/exilio de opositor → escándalo + presión internacional.

---

## 11.7 — Sucesión política

Cuando termina el mandato del jugador:
- Sucesor elegido del partido o aliado.
- Si jugador eligió "designar sucesor": ese asume.
- Si elecciones: opositor con mejor popularidad gana.

Modo "Sucesor": jugador pasa a controlar el siguiente líder, viendo herencia.

---

## 11.8 — Genealogía política

- Cada agente tiene `mentor_id` y `protégés`.
- Asesinar al mentor → sus protégés se vuelven hostiles.
- Promover a un protégé → su mentor te apoya más.

---

## 11.9 — Carrera política del jugador (modo extendido)

Modo "Carrera":
- Empezás como alcalde de una ciudad.
- Subís a gobernador, diputado, senador, ministro, presidente.
- Cada nivel tiene mecánicas reducidas.
- Tu reputación histórica te abre puertas o las cierra.

---

## 11.10 — Validación
- 15+ ministros nombrados con stats.
- 5+ generales nombrados.
- 100 legisladores generados.
- 7-9 jueces nombrados.
- Sistema de votos legislativos consistente con suma de individuales.
- Performance: 200+ agentes simulados sin lag.
