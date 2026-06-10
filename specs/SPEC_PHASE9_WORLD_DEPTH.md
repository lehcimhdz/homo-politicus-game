# SPEC Fase 9 — Datos del mundo (profundización tipo SimCity)

## Objetivo
Llevar el motor de "país como agregado de variables" a "país como mosaico de regiones, ciudades y entidades". SimCity simula edificios y ciudadanos individuales; nosotros podemos simular regiones, industrias y actores con nombre.

---

## 9.1 — Sistema de provincias / regiones

### Modelo
```cpp
struct Region {
    std::string id;
    std::string name;
    int population;
    double regional_gdp;
    double unemployment;
    double poverty_rate;
    double infra_quality;       // 0-1
    double governor_loyalty;    // -1 a +1
    double separatism_pressure; // 0-1
    bool   in_crisis;
    std::vector<std::string> dominant_industries;
};
```

Country pasa a tener `std::vector<Region> regions;` de 10-30 elementos.

### Mecánica
- GDP nacional = suma de regional_gdp + multiplicador.
- Desempleo nacional = promedio ponderado.
- Federal_budget_disparity se calcula real (no es input arbitrario).
- Si regional_gdp cae 30% bajo promedio → separatism_pressure sube.
- Gobernador con loyalty < -0.5 desafía al centro.

### Comandos nuevos
- `transfer_funds <region> <amount>`: redistribución fiscal.
- `appoint_governor <region>`: nombrar gobernador afín (consume mandato).
- `inspect_region <region>`: ver detalles.

### Crisis posibles
- Sublevación regional armada.
- Bloqueo de carreteras de una región.
- Boom económico regional descompensa.
- Migración interna masiva entre regiones.

---

## 9.2 — Ciudades y urbanización

Sub-estructura de Region:
```cpp
struct City {
    std::string name;
    int population;
    double urban_quality;       // Servicios, calles, parques
    double crime_rate;
    double cost_of_living;
    bool is_capital;
    bool slum_growth;
};
```

Ciudades dominantes acumulan poder lobby, ciudades chicas son fuente de descontento si federal_budget_disparity > umbral.

---

## 9.3 — Industrias específicas (no agregados)

Reemplazar `industrial_power`, `agricultural_power`, etc. por industrias granulares:

```cpp
struct Industry {
    std::string name;            // "Mining", "Soy", "Textiles", "Tech", "Pharma"
    double gdp_share;            // % del PIB nacional
    int employment;
    double export_share;
    double lobby_strength;
    double subsidy_level;        // Por el estado
    double tax_burden;
    double pollution_output;
};
```

10-15 industrias por país.

### Mecánica
- Sumatoria de gdp_share ≈ 100%.
- Sector pulled-down por tariff, subsidiado por intervenciones.
- Cada industria tiene un lobby que presiona políticas.

### Comandos nuevos
- `subsidize <industry>`: gasta presupuesto, gana popularidad sectorial.
- `tax_industry <industry>`: ingresos extra, lobby protesta.
- `nationalize <industry>`: acción autoritaria fuerte.

---

## 9.4 — Empresas como entidades (top 20)

Top 20 corporaciones del país tienen nombre, dueño, sector, market_cap.

```cpp
struct Corporation {
    std::string name;
    std::string industry_id;
    double market_cap;
    double political_influence;
    std::string owner_name;
    bool foreign_owned;
    bool nationalized;
};
```

### Mecánica
- Corporaciones financiar campañas (oponentes o jugador).
- Si nationalizás una corp foreign-owned: international_pressure ++.
- Las top 5 votan en el "lobby empresarial" cada turno.

---

## 9.5 — Sindicatos como entidades

```cpp
struct Union {
    std::string name;
    std::string sector;
    int members;
    double militancy;            // 0-1
    double alignment_with_government; // -1 a +1
};
```

Sindicato con militancy > 0.7 y alignment < -0.5 → convoca huelga sectorial.

---

## 9.6 — Validación
- Sumatoria de region.population = country.population.
- Sumatoria de industry.gdp_share ≈ 1.0 (tolerancia 5%).
- Top 20 corporaciones se identifican unívocamente.
- Sin regiones huérfanas (toda región tiene gobernador).
- Performance: 30 regiones × 15 industrias × 20 corps × 10 unions = 9000 entidades. Debe correr a 60fps.
